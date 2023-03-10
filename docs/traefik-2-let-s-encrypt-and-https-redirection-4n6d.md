# Traefik 2:让我们加密和 HTTPS 重定向

> 原文：<https://dev.to/jadekharats/traefik-2-let-s-encrypt-and-https-redirection-4n6d>

# Traefik 2:让我们加密并重定向 HTTPS

本文跟随这些文章:

*   [练习 2:概念](https://dev.to/jadekharats/traefik-2-concept-3elj)
*   [Traefik 2:贡献和调试](https://dev.to/jadekharats/traefik-contribution-and-debug-57ip)

## 需要

我需要:

*   发布 HTTPS 网站
*   持有有效证件
*   重定向 HTTPS 上的 HTTP 流。

## 让我们加密

所以我们把`Let's encrypt`加为 Traefik 的`certificatesResolvers`。
为此，我们需要:

*   `Let's encrypt`的联系人电子邮件地址
*   选择证书存储的格式和文件
*   选择`Let's encrypt`用于创建和更新的挑战模式。

在我的例子中，我使用与 Traefik V1 相同的`acme.json`文件。如果你从零开始，你可以创建一个空白。
对于挑战模式，我更喜欢 HTTP，因为它允许我从 Traefik 做任何事情。

现在我可以将我的块添加到文件
中

```
certificatesResolvers:
  letsencrypt:
    acme:
      email: "jade.kharats@example.tld"
      storage: acme.json
      httpChallenge:
        entryPoint: http 
```

为了在我的 Traefik 实例之间共享这个文件，我需要将它添加到卷中的`stack-traefik.yml`

```
services:
  traefik:
    volumes:
      - /swarm/traefik2/acme.json:/acme.json 
```

从现在起，Traefik 将为所请求的域生成一个证书。让我们去验证我们的仪表板。

## TLS

我会在`entryPoint` https 上加一个`router`。在这个`router`上，我将启用 TLS 并告诉它使用`Let's encrypt`作为解析器。

事情发生在`stack-traefik.yml`

```
services:
  traefik:
    deploy:
      labels:
        - "traefik.http.routers.traefik-router.entrypoints=https"
        - "traefik.http.routers.traefik-router.rule=Host(`traefik.example.tld`)"
        - "traefik.http.routers.traefik-router.tls=true"
        - "traefik.http.routers.traefik-router.tls.certresolver=letsencrypt" 
```

在部署了新版本之后，我用一个有效的证书在 https 中服务了我的域。简单，不是吗？

## HTTPS 重定向

既然我们有 HTTPS 可用，为什么还要继续允许 HTTP 访问呢？我们需要改变水流的方向。Traefik 2 为此提供了一个中间件。

另一个标签放入我们的`stack-traefik.yml`

```
services:
  traefik:
    deploy:
      labels:
        - "traefik.http.routers.traefik-router0.entrypoints=http"
        - "traefik.http.routers.traefik-router0.rule=Host(`traefik.example.tld`)"
        - "traefik.http.routers.traefik-router0.middlewares=traefik-redirectscheme,auth"
        - "traefik.http.middlewares.traefik-redirectscheme.redirectscheme.scheme=https" 
```

所以我们添加了`"traefik.http.middlewares.traefik-redirectscheme.redirectscheme.scheme=https"`来定义重定向。
我把这个中间件叫做`traefik-redirectscheme`，但是我冒险把它用于 Traefik 之外的其他`routers`。这一次我会改名字。
然后我将这个中间件添加到我的`router0`
的中间件列表中，瞧。

# 结论

使用 Traefik 2 很容易管理 HTTPS 和 TLS 部分。

第二个版本的最大优点之一是增加了 TCP 管理。在下面的文章中，我将使用一个 HTTPS 路由器和一个 TCP 路由器建立一个 GITEA 服务器。

traefik.yaml

```
global:
  checkNewVersion: true
  sendAnonymousUsage: true
api:
  dashboard: true
  debug: true
entryPoints:
  ssh:
    address: ":22"
  http:
    address: ":80"
  https:
    address: ":443"
providers:
  docker:
    watch: true
    swarmMode: true
    useBindPortIP: true
    endpoint: "unix:///var/run/docker.sock"
certificatesResolvers:
  letsencrypt:
    acme:
      email: "jade.kharats@example.tld"
      storage: acme.json
      httpChallenge:
        entryPoint: http
log:
  filePath: "log/traefik.log"
  level: WARN
accessLog:
  filePath: "log/access.log"
  bufferingSize: 100 
```

stack-traefik.yml

```
version: "3.3"

networks:
  traefik-net:
    external: true

configs:
  traefik.yaml:
    file: ./traefik.yaml

services:
  traefik:
    image: traefik:v2.0
    ports:
      - 80:80
      - 443:443
      - 22:22
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /swarm/traefik2/acme.json:/acme.json
      - /swarm/traefik2/log:/log
    configs:
      - source: traefik.yaml
        target: /etc/traefik/traefik.yaml

    networks:
      - traefik-net
    deploy:
      mode: global
      placement:
        constraints: [node.role == manager]
      labels:
        - "traefik.docker.network=traefik-net"
        - "traefik.http.routers.traefik-router0.entrypoints=http"
        - "traefik.http.routers.traefik-router0.rule=Host(`traefik.example.tld`)"
        - "traefik.http.routers.traefik-router0.middlewares=traefik-redirectscheme,auth"
        - "traefik.http.routers.traefik-router.entrypoints=https"
        - "traefik.http.routers.traefik-router.rule=Host(`traefik.example.tld`)"
        - "traefik.http.routers.traefik-router.tls=true"
        - "traefik.http.routers.traefik-router.tls.certresolver=letsencrypt"
        - "traefik.http.routers.traefik-router.middlewares=auth"
        - "traefik.http.middlewares.traefik-redirectscheme.redirectscheme.scheme=https"
        - "traefik.http.middlewares.auth.basicauth.users=jade:$$2y$$..."
        - "traefik.http.services.traefik-service.loadbalancer.server.port=8080" 
```