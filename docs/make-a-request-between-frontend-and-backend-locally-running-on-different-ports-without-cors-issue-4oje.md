# 在不同端口上本地运行的前端和后端之间发出请求，而不会出现 CORS 问题

> 原文：<https://dev.to/nikhilponnuru/make-a-request-between-frontend-and-backend-locally-running-on-different-ports-without-cors-issue-4oje>

每当你在同一个系统的两个不同的端口运行前端和后端，你可能很快就会发现自己面临一个 CORS 错误。

#### 什么是 CORS？

来自 MDN

> 跨源资源共享(CORS)是一种机制，它使用额外的 HTTP 头来告诉浏览器，让在一个源(域)上运行的 web 应用程序有权从不同源的服务器访问选定的资源。当 web 应用程序请求的资源的来源(域、协议和端口)不同于它自己的来源时，它会执行跨来源 HTTP 请求。

#### 什么是起源？

> Web 内容的来源由用于访问它的 URL 的方案(协议)、主机(域)和端口来定义。只有当方案、主机和端口都匹配时，两个对象才具有相同的原点。

例如:如果你从 www.abc.com 的[站点向 www.xyz.com](http://www.abc.com)[的](http://www.xyz.com)站点请求一个资源，由于来源不同，这将导致浏览器调用 CORS。

### 本地运行前端和后端服务器:

当在您的系统上本地运行一个项目时，您可能需要启动两个前端的服务器(如 nodejs 等..)和后端(如 python、go-lang 等..).要在同一系统上运行这两台服务器，您需要为它们分配两个不同的端口。(因为一个端口一次只能容纳一个进程)。

例如，前端服务器可以在 localhost:8001 上运行，后端服务器可以在 localhost:8002 上运行。

每当我们从 localhost:8001(前端)向 localhost:8002(后端)发出请求时，由于它们都是两个不同的来源，就会导致 CORS 问题。如何解决这个问题？

处理这种情况的一些方法:

1) **服务器端**:勾选[这个](https://enable-cors.org/server.html)。在来自不同服务器的响应中使用“Access-Control-Allow-Origin”报头来告诉浏览器当前的跨源请求是允许的。(请记住，CORS 是一种使用 http 头告诉浏览器对资源的跨源请求是正确的并且可以被允许的方式)

2) **客户端**:检查[这个](https://www.html5rocks.com/en/tutorials/cors//)。

3) **使用反向代理** :
我已经在这里解释了什么是反向代理[。在进一步阅读之前，请务必检查一下。](https://dev.to/nikhilponnuru/understanding-proxy-reverse-proxy-vpn-load-balancer-and-more-2p98)

我觉得使用像 Nginx 这样的反向代理是一个比上面两种方式更好的解决方案。

### 针对此用例使用反向代理的步骤:

我已经使用 Nginx 测试了以下步骤。

步骤 1)在端口 8001 运行前端。

步骤 2)在端口 8002 运行后端。

步骤 3)假设所有后端请求都通过“/api/”或任何其他类似的分离方式与前端 URL 分离。

步骤 4)在端口 8000 运行反向代理

Nginx 配置示例:服务器模块

```
server {
        listen       8000; 
        server_name  localhost;
        location / {
       proxy_pass http://localhost:8001;
        }
        location /api/ {
           proxy_pass http://localhost:8002;

        }
} 
```

如果你不熟悉反向代理，查看我的[帖子](https://dev.to/nikhilponnuru/understanding-proxy-reverse-proxy-vpn-load-balancer-and-more-2p98)解释它。

现在，使用反向代理的好处是你可以在同一个端口 8000 上运行前端和后端。但是我们不是学到了单个端口被绑定到一个进程吗？每当你用`localhost:8000/api/*`向端点发出请求时，反向代理会将其重定向到 8002(后端),所有没有“/api”的请求都会被重定向到 8001(前端)。

这解决了 CORS 问题，因为原点是一样的，这里是 8000。