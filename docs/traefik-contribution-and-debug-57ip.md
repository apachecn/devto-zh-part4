# Traefik 2:贡献和调试

> 原文：<https://dev.to/jadekharats/traefik-contribution-and-debug-57ip>

# Traefik 2:贡献与调试

这篇文章是这篇文章的后续:[https://dev.to/jadekharats/traefik-2-concept-3elj](https://dev.to/jadekharats/traefik-2-concept-3elj)

## 需要

我们需要在 Traefik 中添加其他服务，尤其是要理解它是如何学习的。

## 调试和记录

一切都将在静态配置中完成。另外我给你静态配置的官方文档的链接 by file:[https://docs . traefik . io/v 2.0/reference/static-configuration/file/](https://docs.traefik.io/v2.0/reference/static-configuration/file/)

### 日志激活

在`traefik.yaml`文件中，我们将添加该块

```
log:
  filePath: "log/traefik.log"
  level: WARN 
```

WARN 级别足以让我的品味理解 Traefik 2 元素的创造和配置。

我们还将添加我们为任何反向代理
找到的`access.log`

```
AccessLog:
  filePath: "log/access.log"
  bufferingSize: 100 
```

### 激活 API 的调试模式

要在`log/traefik.log`日志文件中包含 api 动作，修改 API 块如下

```
api:
  dashboard: true
  debug: true 
```

## 回收集装箱外的原木

我们想把原木从集装箱里拿出来。对于这一点，没有什么比在文件`stack-traefik.yml`
中声明卷更简单的了

```
services:
  traefik:
    volumes:
      - /swarm/traefik2/log:/log 
```

## 更新和匿名投稿。

Traefik 2 处于发布候选版本。因此，有必要保持更新，同时允许 Traefik 向维护人员发送匿名数据，以帮助他们开发 RC。
同样，它发生在`traefik.yaml`中。

```
global:
  checkNewVersion: true
  sendAnonymousUsage: true 
```

## 结论。

在这里，我们已经准备好添加 TLS 和证书生成。

至于上一篇文章，我放回去，现阶段文件的状态。

traefik.yaml

```
global:
  checkNewVersion: true
  sendAnonymousUsage: true
api:
  dashboard: true
  debug: true
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
log:
  filePath: "log/traefik.log"
  level: WARN
AccessLog:
  filePath: "log/access.log"
  bufferingSize: 100 
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
      - /swarm/traefik2/log:/log
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
        - "traefik.http.routers.traefik-router0.rule=Host(`traefik.example.tld`)"
        - "traefik.http.routers.traefik-router0.middlewares=auth"
        - "traefik.http.middlewares.auth.basicauth.users=jade:$$2y$$..."
        - "traefik.http.services.traefik-service.loadbalancer.server.port=8080" 
```