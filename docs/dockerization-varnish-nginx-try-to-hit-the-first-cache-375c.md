# Dockerization: Varnish、Nginx &尝试命中第一个缓存

> 原文：<https://dev.to/vuong/dockerization-varnish-nginx-try-to-hit-the-first-cache-375c>

## 简介

Varnish 有助于减少首次响应后通过缓存响应对 web 服务器的直接请求，帮助您的网站更好地运行。您可以配置缓存路径、要忽略的内容以及如何清除/禁止缓存。

我喜欢 Docker，我在 Docker 上运行我的网站，由 Docker-Compose 提供多种服务，我只是一个开发人员，不像 DevOps 那样了解系统，我想用简单的方式运行它。

我想提一下在线 NGINX 和 varnish。这意味着 NGINX 将覆盖后端并连接到 varnish 来处理响应。

## 必需品

*   Nginx 已经覆盖了 web，在安装清漆之前它仍然在运行
*   Nginx 和 varnish 必须在同一个内部网络上

## 备注

*   Nginx 将使用端口 8080 运行
*   清漆将从端口 80 流出
*   清漆不能处理 SSL。如果你需要处理这个协议，这篇文章不能帮你更多
*   属性构建:`../nginx-server`包括 NGINX 配置，你可以直接使用镜像。应该在你方控制属性构建:`../varnish`包括清漆配置。我将只解释这一部分

## Docker 图片

清漆 docker 图像:[https://github.com/newsdev/docker-varnish](https://github.com/newsdev/docker-varnish)(它已经包括了设置和运行 docker 图像的方法，我只是为 docker-compose 配置写了更多)

engine x:[https://hub . docker . com/_/engine/](https://hub.docker.com/_/nginx/)

## 设置

*   创建`../varnish`文件夹
*   访问创建的文件夹并制作`Dockerfile`

```
FROM newsdev/varnish:4.1.0
ENV VARNISH_MEMORY 100m 
```

Enter fullscreen mode Exit fullscreen mode

*   在同一个文件夹中创建`default.vcl`，这是清漆配置

```
vcl 4.0;
backend default {
  .host = "nginx-server";
  .port = "80";
}

# If you don't include below, header Age in response to client always be 0

sub vcl_deliver {
  # Display hit/miss info
  if (obj.hits > 0) {
    set resp.http.V-Cache = "HIT";
  }
  else {
    set resp.http.V-Cache = "MISS";
  }
  set resp.http.Access-Control-Allow-Origin = "*";
  set resp.http.Access-Control-Allow-Headers = "Accept,Authorization,Cache-Control,Content-Type,DNT,If-Modified-Since,Keep-Alive,Origin,User-Agent,X-Mx-ReqToken,X-Requested-With";
  set resp.http.Allow = "GET, POST";
  set resp.http.Access-Control-Allow-Credentials = "true";
  set resp.http.Access-Control-Allow-Methods = "GET, POST, PUT, DELETE, OPTIONS, PATCH";
  set resp.http.Access-Control-Expose-Headers = "X-Pagination-Total, X-Pagination-Page, X-Pagination-Limit";
}
sub vcl_backend_response {
  if (beresp.status == 200) {
    unset beresp.http.Cache-Control;
    set beresp.http.Cache-Control = "public; max-age=200";
    set beresp.ttl = 200s;
  }
  set beresp.http.Served-By = beresp.backend.name;
  set beresp.http.V-Cache-TTL = beresp.ttl;
  set beresp.http.V-Cache-Grace = beresp.grace;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   修改`docker-compose.yml`的服务配置

```
version: '2'

networks:
  localnetwork:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 162.11.1.0/24
          gateway: 162.11.1.1
services:

  nginx-server:
    container_name: nginx-server
    build: ../nginx-server
    networks:
      - localnetwork
    ports:
      - "8080:80"
  varnish:
    build: ../varnish
    container_name: varnish
    networks:
      - localnetwork
    ports:
      - "80:80"
    depends_on:
      - nginx-server 
```

Enter fullscreen mode Exit fullscreen mode

*   构建和运行 Docker

```
docker-compose build
docker-compose up -d 
```

Enter fullscreen mode Exit fullscreen mode

*   访问 URL 并检查标题

```
http://docker-host-ip:8080/users/{user_id} #Routing to your method, always no cache
http://docker-host-ip/users/{user_id} #Through Varnish first 
```

Enter fullscreen mode Exit fullscreen mode

*   点击 varnish 缓存 URL，查看响应头中的`Age`值:

```
Age = 0, Cache wasn’t HIT (known as MISS)
Age != 0, Cache was HIT (you’re successful) 
```

Enter fullscreen mode Exit fullscreen mode

## 有用的命令

```
# View varnish cache his/miss
docker exec -it varnish bash
varnishncsa -F "%m %U%q ---- %{Varnish:handling}x" 
```

Enter fullscreen mode Exit fullscreen mode