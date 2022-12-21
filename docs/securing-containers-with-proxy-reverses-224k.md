# 用代理反转保护容器

> 原文：<https://dev.to/gsilvapt/securing-containers-with-proxy-reverses-224k>

## 简介

Docker 是一个很棒的工具。有很多资源可以帮助您入门，但是我觉得它们很容易忽略了如何在生产环境中保护您的主机。

这篇文章试图解决一种可能的配置设置，这种设置对外部访问有更多的限制，并且适合所有服务都在 docker 环境下配置的项目。

这里描述的设置是我在一个基于 docker 的服务中使用的，该服务由一个 Redis 缓存数据库、一个 Mongo 数据库、一个 NodeJS + Express RESTful API 和一个用 ReactJs 编写的 web 应用程序组成，这些应用程序在我工作的地方都有。写这篇文章的动机是，有一天，我们发现有人入侵了我们的服务器，并在 Redis keys 中注入了一些 curl 命令。最终，没有数据被泄露，也没有权限被撤销。然而，这引起了我的兴趣，并迫使我回顾我们如何配置我们的堆栈。

## 第 0 课:为所有持久层设置身份验证

Redis，Mongo，MySQL，无论其他选项是什么，它们都需要认证才能写入系统。我们从一开始就这样做了，但是我发现有太多的资源忽略了这一步。您可以使用(并且您可能应该使用)更复杂的选项，比如基于令牌或基于密钥。最起码的是用户名和密码这样简单的东西。哦，请不要选择`123456`作为你的密码。

## 第一课:关闭所有不需要的端口。

在我们最初的方法中，我们只是缺乏经验。我们打开了主机网络中的所有容器端口。如果使用默认端口，任何人都可以访问我们的服务。举个简单的例子:如果你像这样设置你的容器，一些心怀不轨的人很容易发现你的服务，并试图注入或暴力破解任何东西。我们讨论了一下，发现我们只需要端口 80、443 和 22(分别是 HTTP、HTTPs 和 SSH)这样的端口。

## 第 2 课:正确设置容器端口

在意识到我们已经被黑客攻击后，我们开始花更多的时间监控我们的服务，却发现有人试图对我们的 MongoDB 运行查询。这很奇怪，因为据说我们已经关闭了港口。是的，服务需要认证，但是这很烦人。什么也没有发生在他们身上——当它应该发生的时候！

以下是如何在您的主机上正确设置 docker 端口的一些步骤。

> **注意:记住每种情况都有其规格，这并不是设置的唯一方式。这只是一个设置建议，我觉得用起来更安全。**

### Docker-撰写端口配置

大多数教程和指南都忽略了 Docker network，并且没有完全解释在你的主机后台发生了什么。通用撰写:

```
 services:
       webapp:
           ...
           ports:
               - "2019:8080"
       database:
           ...
           ports:
               - "3306:8080" 
```

Enter fullscreen mode Exit fullscreen mode

此配置会在您的主机网络中暴露这两种服务的端口 8080。这允许远程连接。例如，您可以使用`vps-ip:3306`连接到服务。下面是我们如何确定应该配置服务的:

```
 services:
        webapp:
            ...
            ports:
               - "172.17.0.1:2019:8080"
        database:
            ...
            ports:
               - "172.17.0.1:3306:8080" 
```

Enter fullscreen mode Exit fullscreen mode

当你在机器中安装 docker 时，它会自动添加一个网络接口，你可以在 Unix 机器中使用`ip a`来检查。注意有一个条目的 id 是 [`docker0`](https://docs.docker.com/v17.09/engine/userguide/networking/default_network/custom-docker0/) 。它的 IP 通常是上面写的 IP，它不会将该网络暴露给主机的网络。
总之，上述配置确保您的主机可以访问您的服务，但不能从外部访问。您现在可以使用上面的命令`vps-ip:3306`测试连接到服务的尝试。它不会连接。

## 第三课:配置你的防火墙并积极地阻止

如上所述，最讨厌的事情之一是检查日志，看到人们试图运行的东西。这不仅会激励他们继续下去，也会让你不安，因为你知道有人一直在试图攻击你。所以，我的解决方案是积极设置防火墙和 [fail2ban](https://www.fail2ban.org/wiki/index.php/Main_Page) 。

我不会在`iptables`上延伸太多，因为我已经从数字海洋找到了[这篇包含足够信息的文章。](https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server)

总之，这些是最重要的命令:

*   `sudo ufw default deny incoming` -阻止所有外部流量。
*   `sudo ufw allow ssh` -允许 SSH 连接。
*   `sudo ufw app list` -确保`nginx`安装在我们的系统中，然后——`sudo ufw allow 'Nginx Full'`-允许外部和外部的 HTTP/HTTPS 连接。

至于`fail2ban`，5 次重试后决定禁赛 1 周。这是我在`/etc/fail2ban/jail.d/ssh.local` :
中添加的新文件

```
 [sshd]
    maxretries = 5
    bantime = 604800 
```

Enter fullscreen mode Exit fullscreen mode

下一步是使用`fail2ban-client restart`重启 fail2ban 服务来应用规则。请注意，更多的设置可以添加到`fail2ban`中，其中一些可能值得研究。

最后，您可能想知道服务是如何通信的，这就引出了下一个主题:

## 第四课:反向代理一切事物！

我承认我是这个解决方案的粉丝。还有其他方法来配置你的 HTTP 服务器，在这个例子中是[NGiNX——此外还有 nginx](https://nginx.org/) 。下面是告诉 nginx 监听你的应用程序的方法:

```
 server {
        listen 80;
        listen 443 ssl;

        access_log /var/log/nginx/web.access.log;
        error_log /var/log/nginx/web.error.log;

        server_name web.my_domain.app;
        include web.my_domain.app.conf;
    } 
```

Enter fullscreen mode Exit fullscreen mode

我总是发现这样维护`nginx`配置更容易。除了它将特定于服务器的配置从特定于 nginx 的配置中分离出来之外，没有什么特别的原因。如果我得到坏网关，这意味着 nginx 没有正确配置；相反，如果我得到 CORS 错误，我知道服务器配置不佳。

当我们谈论 web 安全时，现在我们总是不得不包括 SSL 证书。我一直推荐使用 [certbot](https://certbot.eff.org/) 和[让我们加密](https://letsencrypt.org/)。他们的网站上真的有所有的说明，并为你做了很多事情。我不会费心手动做这些。不过，我知道有些项目可能需要手工操作。通常，我遵循 Nginx HTTP 服务器的`certbot's`文档。最后，不要忘记添加 cronjob 来自动更新证书。有一些关于你应该多久做一次的讨论，但是选择一些适合你需要的。最后，如果您选择将 HTTP 流量转发到 HTTPS(您的最终用户肯定会喜欢)，您的`/etc/nginx/sites-available/default`应该是这样的:

```
 server {
        if ($host = web.my_domain.app) {
            return 301 https://$host$request_uri;
        } # managed by certbot

        access_log /var/log/nginx/web.access.log;
        error_log /var/log/nginx/web.error.log;

        ssl_certificate /etc/letsencrypt/live/web.my_domain.app/fullchain.pem; # managed by certbot
        ssl_certificate_key /etc/letsencrypt/live/web.my_domain.app/privkey.pem; # managed by certbot

        listen 80;
        listen 443 ssl;
        server_name web.my_domain.app;
        include ssl.conf; # managed by certbot
        include web.my_domain.app.conf;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这就是你的站点配置在`/etc/nginx/web.my_domain.app.conf` :
下的样子

```
 resolver 8.8.8.8;
    add_header 'Access-Control-Allow-Origin'        $http_origin                                            always;
    add_header 'Access-Control-Allow-Methods'       'GET, PUT, POST, OPTIONS'                always;
    add_header 'Access-Control-Expose-Headers'      'authorization, Authorization'                          always;
    add_header 'Access-Control-Allow-Headers'       'Accept, Content-Type'  always;

    if ($request_method = 'OPTIONS') {
        return 204;
    }

    location ~ / {
        proxy_set_header        X-Real-IP          $remote_addr;
        proxy_set_header        X-Forwarded-For    $proxy_add_x_forwarded_for;
        proxy_set_header        X-Forwarded-Host   $host:80;
        proxy_set_header        X-Forwarded-Server $host;
        proxy_set_header        X-Forwarded-Port   80;
        proxy_set_header        X-Forwarded-Proto  https;
        proxy_intercept_errors off;

        proxy_pass_request_headers on;
        proxy_pass http://172.17.0.1:2019;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这是配置本身，设置代理反向，配置允许的头和源(以避免 CORS)。这确保了您的服务只能通过主机的网络进行通信，外部世界永远无法到达它们。

## 最后一分钟检查

如果您想确认您只打开了您指定的端口，您可以运行`netstap -pln`并检查输出。你应该只有端口`80`、`443`和`22`。

## 总结

在这篇文章中，我们学习了如何在服务器中设置 Linux 容器(通过 Docker)和微服务。这种设置强化了您的服务，拒绝外部对它们的访问。但是在内部，只要容器都在同一个网络中，它们就可以相互通信。你可能想知道，如果所有外部流量都被禁用，你如何在 MySQL Workbench 或 Robot3T 等应用程序中访问你的数据库。您可以通过用户名/密码组合使用 SSH 隧道来实现。最好是，做一些适当的 SSH 密钥设置，并用 SSH 密钥来代替。用户名/密码组合总比没有好，但这相当不安全。

感谢你的阅读，请在评论中告诉我你会有什么不同的做法。

有趣的事实:我们最初禁止了 95 个 IP。在违规之后，在这样配置我们的服务之后，我们注意到大约有 300 个 IP 被持续禁止，SSH 服务的访问尝试显著减少。