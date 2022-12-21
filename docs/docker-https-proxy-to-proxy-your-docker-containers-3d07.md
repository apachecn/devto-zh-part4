# 代理你的 docker 容器

> 原文：<https://dev.to/shroomlife/docker-https-proxy-to-proxy-your-docker-containers-3d07>

嘿，

作为一名开发人员，我在我的服务器上使用 docker，有时我希望在一台服务器上运行多个容器，并且它们应该可以通过不同的主机名访问。因为这个代理特性是我唯一的需求，所以我不想使用 Apache 或 Nginx 臃肿的服务。它们有很多特性，但不仅仅是为了拆分才需要它们。

所以我创建了一个简洁的 docker 镜像，它可以很容易地被用作一个主代理容器来代理你的容器中主机名之间的请求。

此外，我想让 HTTPs 为我的代理服务，所以我添加了一个自签名证书，可以轻松地在 Cloudflare 帐户后面使用。如果需要，您也可以添加自己的证书。整件事就是#开源！

github:https://github . com/shroomlfe/docker-https 代理
docker hub:[https://hub . docker . com/r/shroomlfe/docker-https 代理](https://hub.docker.com/r/shroomlife/docker-https-proxy)

### 使用 docker-compose 轻松设置

```
version: '3.3'
services:
  proxy:
    container_name: proxy
    image: shroomlife/docker-https-proxy:latest
    ports:
      - "80:80"
      - "443:443"
  example.com:
    container_name: example.com.proxy
    image: httpd:latest 
```

Enter fullscreen mode Exit fullscreen mode

只需将`.proxy`添加到您的`example.com`域中，您就可以开始了！

在那里，您还可以找到一个有用的自述文件，以便根据自己的需要进行设置。希望能帮助其他开发者轻松解决一个常见问题。

希望听到来自社区的一些反馈！

Best，
shroomli