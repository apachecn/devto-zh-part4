# 第二步:概念

> 原文：<https://dev.to/jadekharats/traefik-2-concept-3elj>

# 教程 2:概念

## 描述

Traefik 是一个反向代理，也是负载平衡器。第一个版本很容易在 docker swarm 上建立。

## 需要

一般来说，我们需要部署容器，并且可以通过域名进行访问。

如果我们分解这个需求，我们有:

*   侦听此 ip 和一个或多个端口的服务
*   证明交易的服务
*   证书管理服务
*   到正确容器的请求重定向服务(HTTP 或 TCP)。

尽管如此，我们有 Traefik v2

## Traefik v2 的元素

### 配置

Traefik 管理两种类型的配置:动态或静态。
对于静态部分，我选择一个`traefik.yaml`文件。
动态部分由位于容器级别的标签管理。

### 提供者

Traefik 需要知道谁将为它提供名为`providers`的动态配置

在我的例子中，它是一个群模式的码头工人。所以我把这个加到`traefik.yaml`

```
providers:
  docker:
    watch: true
    swarmMode: true
    endpoint: "/unix:///var/run/docker.sock" 
```

### 输入类型

入口点是 Traefik 将侦听的 ip:port 对。这是一个静态配置。

```
entrypoints:
  ssh:
    address: ":22"
  http:
    address: ":80"
  https:
    address: ":443" 
```

警告:Traefik 以约定优先于配置的方式工作。如果您没有在路由器上指定入口点，默认情况下，它将从列表中选择第一个。对我来说，我把它们按照端口号的顺序排列，以便于阅读。

### 路由器

路由器可以是静态管理的，但在我的例子中是动态管理的。路由器需要一个或多个入口点和规则来确定应该遵循哪个 Traefik 路由器。

本文的其余部分以 treafik 仪表板展示为例。

我们将在端口 80 上监听头:host ("traefik.example.tld ")中的请求

我们必须将以下标签添加到 traefik 容器中:

```
- "traefik.http.routors.traefik-router0.entryPoints=http"
- "traefik.http.routeurs.traefik-router0.rule=Host(`traefik.example.tld`)" 
```

### 服务

该服务易于配置，因为它是携带标签的容器。Traefik 会从中扣除很多。

现在，我将向您展示如何指定 Traefik 应该在哪个网络上查找容器 ip，以及如何指定端口。

默认情况下，我总是向`traefik-net`网络添加容器，这些容器必须通过 Traefik 公开。在 V1，这是一种后天养成的习惯。

在容器的标签上，我会告诉他取这个网络的 ip 地址

```
- "traefik.docker.network=traefik-net" 
```

然后我会告诉他仪表板服务的端口。但是在这里，Traefik 2 不允许在提供者 docker 没有激活的情况下进行。
在`traefik.yaml`文件中，我修改了`providers`块如下:

```
providers:
  docker:
    watch: true
    swarmMode: true
    useBindPortIp: true
    endpoint: "/unix:///var/run/docker.sock" 
```

这允许添加以下标签

```
- "traefik.http.services.traefik-service.loadbalancer.server.port=8080" 
```

当然，Traefik 也必须被要求展示他的仪表板，这是在`traefik.yaml`
中完成的

```
api:
  dashboard: true 
```

从那里，通过启动 traefik 服务，我们已经可以访问我们的仪表板。但是一切都是公开的。幸运的是，Traefik 提供了一种通过中间件添加中间处理的方法。

### 中间件

有各种各样的，但首先，我会告诉你如何添加一个基本的认证。然后将中间件添加到路由器中。

中间件的配置可以是静态的也可以是动态的。关于在哪里设置这个中间件，我想了很多。然后我告诉自己所有的 Traefik 元素都必须动态配置。

因此，我将这个标签添加到我的容器 Traefik

```
- "traefik.http.middlewares.auth.basicauth.users=titi:$$2y..." 
```

并将其添加到我的路由器

```
- "traefik.http.routeurs.traefik-router0.middlewares=auth" 
```

## 结论

以上是我的 Traefik v2 在我的 Swarm 上的第一次启动，带有基本配置。
[![Dashboard Traefik V2](img/d9ba995b7a9f724a299b66ac8de04725.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--80q05iMr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z2f1brpoxxazgayh8rem.png)

在下一篇文章中，我将详细介绍我的用于 https 重定向和证书管理的`traefik.yaml`和`stack-traefik.yml`文件。
同时，我问你下面两个文件的内容。

traefik.yaml

```
api:
  dashboard: true
entrypoints:
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
```

stack-traefik.yml

```
 version: "3.3"

 Link:
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
     configs:
       - source: traefik.yaml
         target: /etc/traefik/traefik.yaml
     Link:
       - traefik-net
     deploy:
       fashion: global
       placement:
         constraints: [node.role == manager]
       labels:
         - "traefik.docker.network=traefik-net"
         - "traefik.http.routers.traefik-router0.entrypoints=http"
         - "traefik.http.routers.traefik-router0.rule  =  Host  (`  traefik.kharats.fr`)"
         - "traefik.http.routers.traefik-router0.middlewares=auth"
         - "traefik.http.middlewares.auth.basicauth.users=titi:$$2y$$..."
         - "traefik.http.services.traefik-service.loadbalancer.server.port=8080" 
```