# 数据狗日志设置故障排除

> 原文：<https://dev.to/saviour123/troubleshooting-datadog-log-setup-hbj>

## 描述

datadog 的设置对任何人来说都很简单。但是要把它运到原木上可能会很麻烦。本指南将帮助您排除故障。

## 已知问题

安装后，所有配置都正确，包括在您的`datadog.yaml`中设置的`logs_enabled: true`，您的 datadog 日志设置可能仍然不工作。
这些都是经验之谈，你可以在需要的时候参考。

1.  Datadog Log

第一个调用点是 datadog 日志。注意:这些日志不是由 Logrotate 管理的，所以它会变得非常大。我劝你`tail -n 500 /var/log/datadog/agent.log`。

检查此日志中的 TCP(端口)和权限问题。

```
sudo cat /var/log/datadog/agent.log
# or
sudo cat /var/log/datadog/agent.log | grep permission 
```

1.  用户和用户组

根据经验，如果`/etc/datadog/conf.d/awesome-app.d/conf.yaml`由您在安装过程中创建的
dd-agent 用户和组所有，datadog 会工作得很好。
如果不确定，运行`cd /etc/datadog/conf.d/awesome-app && ll`查看 usergrp 和 user。如果该目录归 root 或任何其他用户所有，您可以使用以下命令来修复它。

```
sudo chown dd-agent awesome-app.d/conf.yaml
sudo chgrp dd-agent awesome-app.d/conf.yaml

# and restart datadog

sudo service datadog-agent restart 
```

验证您是否仍然无法发送日志。

1.  授予 Datadog 权限

大多数时候，我们可以幸运地在我们发送到 datadog 的日志文件上运行一些`chmod`。这不太好，因为目录和日志是由不同用户和组的应用程序生成的。

所以这里最好的方法是 [setfacl](https://linux.die.net/man/1/setfacl) 。它授予对文件的权限，而不需要损害其以前的用户或权限。

按照下面的路径执行它

`setfacl -m u:dd-agent:rwx /var/log/awesome-app/file.log`

或者在目录上授予它

`setfacl -m u:dd-agent:rwx /var/log/awesome-app`

重新开始，你会没事的。

更多信息，请访问 https://docs.datadoghq.com/！