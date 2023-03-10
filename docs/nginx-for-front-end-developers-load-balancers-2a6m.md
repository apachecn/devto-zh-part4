# Nginx 面向前端开发人员——负载平衡器

> 原文：<https://dev.to/ganeshmani/nginx-for-front-end-developers-load-balancers-2a6m>

在本文中我们将看到，Nginx 为前端开发人员提供了负载平衡器。这是 Nginx 面向前端开发人员的第三部分。要阅读以前的文章

*   [Nginx 前端开发人员-第一部分](https://cloudnweb.dev/2019/05/nginx-for-front-end-developers/)
*   [Nginx 前端开发人员-第二部分](https://cloudnweb.dev/2019/06/nginx-for-front-end-developers-beyond-the-basics/)

最重要的是，负载平衡器是 Nginx 的一个重要特性。我们将在本文中看到如何配置它。

### 什么是负载平衡？

负载均衡就是将流量分配给不同的服务器。

*   ![](img/ebbb4313a582341a19cf34e3ee891e3a.png)

nginx 主要通过三种机制将请求分发到指定的服务器。

### 负载平衡机制

*   **Round - Robin** -这种机制根据服务器权重将请求分配给服务器。

```
upstream backend {
   # no load balancing method is specified for Round Robin
   server backend1.example.com;
   server backend2.example.com;
}
```

*   **最少连接**——这种机制将请求分配给连接最少的服务器

```
upstream backend {
    least_conn;
    server backend1.example.com;
    server backend2.example.com;
}
```

*   **IP - Hash** -请求发送到的服务器由客户端 IP 地址决定。在这种情况下，要么使用 IPv4 地址的前三个八位字节，要么使用整个 IPv6 地址来计算哈希值。该方法保证来自同一地址的请求到达同一服务器，除非该服务器不可用。

```
upstream backend {
    ip_hash;
    server backend1.example.com;
    server backend2.example.com;
}
```

**上游**是 nginx 配置中用来指定负载均衡器的关键字。

### 在 nginx 中配置负载平衡器

我们将使用 nginx 负载平衡器将请求负载平衡到三个不同的服务器。

此外，创建一个具有不同端口的三节点服务器并运行该服务器。

**第一台服务器** - **app.js**

```
const express = require('express');

const app = express();

app.get('/',(req,res) => {
     res.send("Welcome to Server 1");   
})

app.listen(3000,() => {
    console.log("app is listening to port 3000");
})
```

**第二服务器** - **app.js**

```
const express = require('express');

const app = express();

app.get('/',(req,res) => {
    res.send("Welcome to server 3");
})

app.listen(3001,() => {
    console.log("app is listening to port 3002");
})
```

**第三服务器** - **app.js**

```
const express = require('express');

const app = express();

app.get('/',(req,res) => {
    res.send("app is running in server 2");
});

app.listen(3002,() => {
    console.log("app is listening to port 3001");
})
```

同时，使用以下命令运行所有服务器

```
$ node app.js
```

*   ![](img/9ef562e4381a2d3443ac8e01be99eccc.png)

*   ![](img/2b10bcb62fb72b56de0a87a7a7f44c09.png)

*   ![](img/61a222ca9a7aa4706050bb4c8e729632.png)

现在，服务器正在运行。之后，您需要配置 nginx 来在这些服务器之间实现负载平衡。

我假设你之前已经安装了 nginx 并进行配置。如果您不是，请在继续之前阅读本文。

在**/etc/nginx/sites-available**中创建一个名为 **node-app** 的文件。

(注意:我用的是 Ubuntu，nginx 文件位置对于其他操作系统是不同的)

```
 upstream backend {

  server 127.0.0.1:3000;

  server 127.0.0.1:3001;

  server 127.0.0.1:3002;

 }

server {

     listen 80;

     server_name localhost;

      location / {

          proxy_pass "http://backend";

       }
}
```

您需要注意两个指令。

*   **upstream** - upstream 是定义负载平衡器的指令，包含负载平衡服务器的服务器名称
*   **服务器** -它包含服务器配置和 **proxy_pass** ，后者包含上游负载平衡器

之后，您需要将文件从**站点可用**移动到**站点启用**

```
$ sudo ln -s /etc/nginx/sites-available/node-app /etc/nginx/sites-enabled
```

类似地，重启 nginx 服务

```
$ sudo service nginx restart
```

nginx 服务成功重启后。在浏览器中访问 **http://localhost**

[https://www.youtube.com/embed/RuQ_pPOh4sE](https://www.youtube.com/embed/RuQ_pPOh4sE)

总之，这就是 nginx 如何在不同服务器之间实现负载平衡。

总而言之，本系列文章涵盖了 [Nginx 设置](https://cloudnweb.dev/2019/05/nginx-for-front-end-developers/)、[基本配置](https://cloudnweb.dev/2019/06/nginx-for-front-end-developers-beyond-the-basics/)和 Nginx 负载均衡器

参考:

[官方 Nginx 文档](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)