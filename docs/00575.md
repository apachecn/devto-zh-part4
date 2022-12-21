# 正在检查 Nginx 的状态

> 原文：<https://dev.to/anselmopfeifer/checking-status-for-nginx-4g8l>

在这篇文章中，我将解释我们如何获得 Nginx 配置的状态，有时当后端不工作时，通过 curl 命令过滤 HTTP 代码并不容易，此外这里的意图是获得代理配置的状态。

*   首先，检查状态模块是否已经启用，命令是:
    `nginx -V 2>&1 | grep -o with-http_stub_status_module`，大多数 Linux 发行版都带有启用的 ngx_http_stub_status_module。

*   之后，我们需要在 Nginx 配置文件中启用 stub_status 模块，我们可以创建一个名为`nginx-status.conf`的新文件，或者在现有的代理配置文件中添加以下内容。

```
location /nginx-status {
    stub_status;
    allow 127.0.0.1;    #only allow requests from localhost
    deny all;       #deny all other hosts   
 } 
```

*   在重启 Nginx 之前，我们可以测试上面的配置是否没有破坏什么，为了测试它，运行命令`nginx -t`，如果结果很好，让我们去重新加载服务以应用配置`nginx -s reload`。

*   要测试状态结果，请转到[http://127 . 0 . 0 . 1/nginx-status](http://127.0.0.1/nginx-status)，结果必须如下所示:

```
Active connections: 1 
server accepts handled requests
 15 15 27 
Reading: 0 Writing: 1 Waiting: 0 
```

PS:是不是有什么没用？不要责怪我，看看这个链接:[更多关于 Nginx 的信息在这里:)](https://dev.to/search?q=nginx%20)