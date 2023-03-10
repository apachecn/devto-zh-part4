# 使用 webhooks 更新自我托管的 Jekyll 博客

> 原文：<https://dev.to/severo/using-webhooks-to-update-a-self-hosted-jekyll-blog-59al>

我在我的个人服务器上主持一个社区项目的[博客。这是一个用](https://atlas.tecnologia.bo/) [Jekyll](https://jekyllrb.com/) 生成的静态网站，内容在 [git 库](https://github.com/RipeAtlasBolivia/atlas.tecnologia.bo)中进行版本化。

在这篇博文中，我解释了我是如何让博客在每次提交时自动更新的。这和 [Github Pages](https://pages.github.com/) 的想法是一样的，但是是针对自托管的。

## TL；速度三角形定位法(dead reckoning)

*   创建一个脚本来克隆 git 存储库，然后构建并部署网站
*   在服务器上发布 webhooks 端点
*   配置 GitHub 存储库，在每次新提交时发送一个 webhook

## 部署脚本

网站托管在 Debian 服务器上，文件从`/var/www/atlas.tecnologia.bo/`目录提供。要从命令行更新它，首先安装 [Jekyll 需求](https://jekyllrb.com/docs/installation/ubuntu/)，然后创建并启动 bash 脚本:

```
$ /opt/deploy_atlas.tecnologia.bo.sh 
```

其中包含:

```
#!/usr/bin/env bash

# Clone git repository
rm -rf /tmp/atlas.tecnologia.bo
git clone https://github.com/RipeAtlasBolivia/atlas.tecnologia.bo.git /tmp/atlas.tecnologia.bo

# Generate blog
cd /tmp/atlas.tecnologia.bo
bundle install bundle exec jekyll build --source /tmp/atlas.tecnologia.bo/ --destination /var/www/atlas.tecnologia.bo/

# Clean
rm -rf /tmp/atlas.tecnologia.bo 
```

## Webhook 守护进程

与其手动启动脚本，不如让 webhook 守护进程在每次接收到端点上的 HTTP 请求时完成这项工作。

首先安装 [webhook 包](https://packages.debian.org/buster/webhook) :

```
$ sudo apt install webhook 
```

验证服务正在运行:

```
$ sudo service webhook status
● webhook.service - Small server for creating HTTP endpoints (hooks)
   Loaded: loaded (/etc/systemd/system/webhook.service; enabled; vendor preset: enabled)
   Active: active (running) since Mon 2019-08-12 08:25:24 UTC; 2 weeks 0 days ago
     Docs: https://github.com/adnanh/webhook/
 Main PID: 419 (webhook)
    Tasks: 6 (limit: 4697)
   Memory: 4.8M
   CGroup: /system.slice/webhook.service
           └─419 /usr/bin/webhook -verbose -hooks /etc/webhook.conf 
```

我们配置 webhook 守护进程，创建包含以下内容的文件`/etc/webhook.conf`(参见 [webhook 文档](https://github.com/adnanh/webhook/wiki/)了解更多细节):

```
[  {  "id":  "atlas",  "execute-command":  "/opt/deploy_atlas.tecnologia.bo.sh",  "command-working-directory":  "/tmp/",  "trigger-rule":  {  "match":  {  "type":  "payload-hash-sha1",  "secret":  "xxxxxxxxx",  "parameter":  {  "source":  "header",  "name":  "X-Hub-Signature"  }  }  }  }  ] 
```

这将创建一个名为`atlas`的新钩子，当在`http://localhost:9000/hooks/atlas`上接收到 GET 或 POST 请求时，这个钩子将被触发。当被触发时，它将检查是否提供了有效的`X-Hub-Signature` HTTP 头，然后从`/tmp`工作目录启动`/opt/deploy_atlas.tecnologia.bo.sh`脚本。

试试看:

*   计算对应于`xxxxxxxxx`秘密的`X-Hub-Signature`

```
$ echo -n "" | openssl sha1 -hmac "xxxxxxxxx"
(stdin)= d46d87941e6f285be78ff0f1c8ea32620577b9ef 
```

*   请求端点:

```
$ curl -X POST -H "X-Hub-Signature: sha1=d46d87941e6f285be78ff0f1c8ea32620577b9ef" -v http://localhost:9000/hooks/atlas
...
> POST /hooks/atlas HTTP/1.1
> Host: localhost:9000
> User-Agent: curl/7.64.0
> Accept: */*
> X-Hub-Signature: sha1=d46d87941e6f285be78ff0f1c8ea32620577b9ef
>
< HTTP/1.1 200 OK
< Date: Mon, 26 Aug 2019 13:45:09 GMT
< Content-Length: 0
< 
```

*   检查日志:

```
$ grep webhook /var/log/syslog
Aug 26 14:06:40 webhook[10552]: [webhook] 2019/08/26 14:06:40 Started POST /hooks/atlas
Aug 26 14:06:40 webhook[10552]: [webhook] 2019/08/26 14:06:40 [d2ae30] incoming HTTP request from [::1]:58800
Aug 26 14:06:40 webhook[10552]: [webhook] 2019/08/26 14:06:40 [d2ae30] atlas got matched
Aug 26 14:06:40 webhook[10552]: [webhook] 2019/08/26 14:06:40 [d2ae30] atlas hook triggered successfully
Aug 26 14:06:40 webhook[10552]: [webhook] 2019/08/26 14:06:40 Completed 200 OK in 670.302µs
Aug 26 14:06:40 webhook[10552]: [webhook] 2019/08/26 14:06:40 [d2ae30] executing /opt/deploy_atlas.tecnologia.bo.sh (/opt/deploy_atlas.tecnologia.bo.sh) with arguments ["/opt/deploy_atlas.tecnologia.bo.sh"] and environment [] using /tmp/ as cwd
Aug 26 14:06:46 webhook[10552]: [webhook] 2019/08/26 14:06:46 [d2ae30] command output: Cloning into '/tmp/atlas.tecnologia.bo'...
...
Aug 26 14:06:46 webhook[10552]: Bundle complete! 4 Gemfile dependencies, 24 gems now installed.
Aug 26 14:06:46 webhook[10552]: Use `bundle info [gemname]` to see where a bundled gem is installed.
Aug 26 14:06:46 webhook[10552]: Configuration file: /tmp/atlas.tecnologia.bo/_config.yml
Aug 26 14:06:46 webhook[10552]:             Source: /tmp/atlas.tecnologia.bo/
Aug 26 14:06:46 webhook[10552]:        Destination: /var/www/atlas.tecnologia.bo/
Aug 26 14:06:46 webhook[10552]:  Incremental build: disabled. Enable with --incremental
Aug 26 14:06:46 webhook[10552]:       Generating...
Aug 26 14:06:46 webhook[10552]:        Jekyll Feed: Generating feed for posts
Aug 26 14:06:46 webhook[10552]:                     done in 0.45 seconds.
Aug 26 14:06:46 webhook[10552]:  Auto-regeneration: disabled. Use --watch to enable.
Aug 26 14:06:46 webhook[10552]: [webhook] 2019/08/26 14:06:46 [d2ae30] finished handling atlas 
```

注意:200 HTTP 代码仅仅意味着已经为`atlas`标识符找到了挂钩，并且 X-Hub-Signature 是有效的。它没有说明部署脚本是成功还是失败。

为了从 Apache web 服务器发布端点，在 Apache 配置中配置一个反向代理:

```
ProxyPass /webhook/ http://localhost:9000/hooks/
ProxyPassReverse /webhook/ http://localhost:9000/hooks/ 
```

## GitHub 配置

最后，为了在每次新推送时触发 webhook，请转到 GitHub 项目设置，然后选择“Webhooks”，并使用以下参数创建一个新的 webhook:

*   有效负载 URL: `https://mydomain/webhook/atlas`
*   内容类型:`application/json`
*   秘密:`xxxxxxxxx`
*   您希望哪个事件触发此 webhook？:`Just the push event.`

## 用 GitLab 替代配置

如果您的 git 存储库托管在 GitLab 实例上，您必须更改`/etc/webhook.conf`文件中的头检查:

```
[  {  "id":  "atlas",  "execute-command":  "/opt/deploy_atlas.tecnologia.bo.sh",  "command-working-directory":  "/tmp/",  "trigger-rule":  {  "match":  {  "type":  "value",  "value":  "xxxxxxxxx",  "parameter":  {  "source":  "header",  "name":  "X-Gitlab-Token"  }  }  }  }  ] 
```

注意:请求中提供的`X-Gitlab-Token`头将包含值`xxxxxxxxx`，而不是 GitHub 中的 HMAC。因此，为了模拟这个请求，将 curl 命令修改为:

```
$ curl -X POST -H "X-Gitlab-Token: xxxxxxxxx" -v http://localhost:9000/hooks/atlas 
```

然后，GitLab 项目配置类似于 GitHub:转到项目“设置”、“集成”，添加一个 webhook，包含:

*   网址:`https://mydomain/webhook/atlas`
*   秘密令牌:`xxxxxxxxx`
*   触发器:[x]推送事件

## 参考文献

*   [https://www.rasmuslarsson.se/post/hugo.html](https://www.rasmuslarsson.se/post/hugo.html)
*   [https://github.com/adnanh/webhook/](https://github.com/adnanh/webhook/)
*   [https://jekyllrb.com/](https://jekyllrb.com/)