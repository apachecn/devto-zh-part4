# 如何集中日志:Linux 系统日志

> 原文：<https://dev.to/sematext/how-to-centralize-logs-linux-system-journal-jpa>

您知道大多数 Linux 系统都有完整的日志管理解决方案吗？基于`systemd`的分布包含`journald`和`journalctl`。

**systemd-journal d**——所有的 Linux 系统进程都将日志写入系统日志，由`journald`管理。系统日志是本地日志存储。

**journalctl** *是显示日志的命令行客户端，具有各种过滤选项，如时间、系统单位或日志事件中存储的任何其他字段。对于高级搜索，可以通过管道将输出传递到 grep，这使得将复杂的搜索表达式应用到 journalctl 输出变得容易。
journal CTL 客户端不仅对日志搜索有用，它还提供了各种其他功能，例如管理系统日志存储。

**systemd-journal-upload** 是一项将日志事件转发到远程`journald`实例的服务。在所有 Linux 机器上配置`journal-upload`将日志事件转发到中央日志服务器是集中日志的最佳方式。然后可以使用中央日志服务器上的`journalctl`进行日志搜索。尽管 Linux 控制台很酷，但您需要一个 web UI 来搜索日志和可视化提取的数据，以便更容易和更实际地进行故障诊断。

将日志发送到弹性堆栈是集中日志的一种常见做法，但是如何用 journald 实现这一点呢？

与 rsyslog 不同，journald 没有将日志直接转发到 Elasticsearch 的选项。因为我们需要 json 数据，`journalctl -o json`的输出可能会有用。将 journalctl 的输出管道化到 Logagent 可能是一个解决方案:

`journalctl -o json -f | logagent -i mylogs -u http://elasticsearch:9200`

酷，它的工作！然而，正在运行的进程在启动时无法捕获日志，或者在重启后无法正常恢复——我们可能会丢失一些日志——一点也不！

幸运的是，[日志代理](https://sematext.com/logagent/)有一个[插件](https://sematext.com/docs/logagent/input-plugin-journald-upload/)，它从`systemd-journal-upload`服务接收日志。让我们从头开始，设置 Logagent 来接收 journald 日志，并将它们存储在 Elasticsearch 或 Sematext Cloud 中。

[![](img/5d9ce9e01baf5027a743f4c53c13a269.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5ZqRXuBm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/systemd-journal-upload.png)

## 将 Logagent 设置为本地日志中心

要运行 Logagent，您首先需要一个 Logs 应用程序令牌。如果你还没有 Sematext 日志应用程序，你现在可以[创建日志应用程序](https://apps.sematext.com/ui/integrations)。
然后你可以[安装日志代理](https://sematext.com/docs/logagent/installation/)。默认设置将日志文件从/var/log 发送到 Sematext Cloud。要运行 Logagent，您需要一个 Logs 应用程序令牌。

要从`journal-upload`服务接收日志，激活`/etc/sematext/logagent.conf`中的插件。

```
# Global options
options:
  includeOriginalLine: true

input:
   journal-upload:
    module: input-journald-upload
    port: 9090
    worker: 0
    systemdUnitFilter: 
      include: !!js/regexp /.*/i
    # exclude: !!js/regexp /docker|containerd/i
    # add static tags to every log event 
    tags:
     log_shipper: logagent
     # _index is special tag for log routing with elasticsearch output-plugin
     # Set the index name here in case journald logs should be 
     # stored in a separate index
     # _index: MY_INDEX_FOR_ELASTICSEARCH_OUTPUT or 
     #         YOUR_SEMATEXT_LOGS_TOKEN_HERE
     # you can add any other static tag 
     # node_role: kubernetes_worker
     # journald might provide many fields, 
     # to reduce storage usage you can remove redundant fields
    removeFields:
      - __CURSOR
      - __REALTIME_TIMESTAMP
      - _SOURCE_REALTIME_TIMESTAMP
      - __MONOTONIC_TIMESTAMP
      - _TRANSPORT

output: 
  # output data for debugging on stdout in YAML format
  # stdout: yaml
  sematext-cloud:
    module: elasticsearch
    url: https://logsene-receiver.sematext.com
    # url: https://logsene-receiver.eu.sematext.com
    index: YOUR_SEMATEXT_LOGS_TOKEN_HERE 
```

现在我们可以用
重启`logagent`服务

```
systemctl restart logagent 
```

太好了，我们的日志中心正在运行。

让我们继续在我们的 Linux 服务器上设置`systemd-journal-upload`。

# 设置系统 d-日志-上传

**注意:请注意，该示例使用 127.0.0.1 作为 Logagent 的 IP 地址。您应该将 127.0.0.1 替换为运行 logagent 的服务器的实际 IP 地址。**

使用下面的命令安装`systemd-journal-remote`

```
sudo apt-get install systemd-journal-remote 
```

编辑`/etc/systemd/journal-upload.conf`并更改 URL 属性。

```
[Upload]
URL=http://127.0.0,1:9090
# ServerKeyFile=/etc/ssl/private/journal-upload.pem
# ServerCertificateFile=/etc/ssl/certs/journal-upload.pem
# TrustedCertificateFile=/etc/ssl/ca/trusted.pem 
```

这将确保`journal-upload`在引导时启动。

请注意，如果创建 HTTP 连接不起作用，上传服务可能会停止。如果发生这种情况，服务会将当前光标位置存储在系统日志中。因此，您应该在服务定义中设置有用的重启选项。

编辑`/etc/systemd/system/multi-user.target.wants/systemd-journal-upload.service`以更改重启选项。

```
[Unit]
Description=Journal Remote Upload Service
Documentation=man:systemd-journal-upload(8)
After=network.target

[Service]
ExecStart=/lib/systemd/systemd-journal-upload \
          --save-state
User=systemd-journal-upload
SupplementaryGroups=systemd-journal
PrivateTmp=yes
PrivateDevices=yes
#WatchdogSec=3min
Restart=always
TimeoutStartSec=1
TimeoutStopSec=1
StartLimitBurst=1000
StartLimitIntervalSec=5
# If there are many split up journal files we need a lot of fds to
# access them all and combine
LimitNOFILE=16384
[Install]
WantedBy=multi-user.target 
```

应用更改并重启`journal-upload` :

```
systemctl daemon-reload
sudo systemctl enable systemd-journal-upload.service 
```

打开你的日志应用程序，检查你的日志是否到达 Sematext Cloud。
下面的[视频](https://www.youtube.com/embed/glwZ8OCV0kc?list=PLT_fd32OFYpfLBFZz_HiafnqjdlTth1NS)展示了如何使用语义用户界面。

[https://www.youtube.com/embed/glwZ8OCV0kc](https://www.youtube.com/embed/glwZ8OCV0kc)