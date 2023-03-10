# 在一台机器上自托管多个应用程序

> 原文：<https://dev.to/ganesshkumar/self-hosting-multiple-applications-in-a-single-machine-1l3o>

我喜欢自托管的个人应用程序。我在云上旋转了一台机器，开始托管我的应用程序。当我想托管我的第二个应用程序时，我发现无需在 URL 中指定端口号就可以将两个应用程序的请求路由到同一台机器。在 URL 中指定一个端口号，比如[http://miniflux.ganesshkumar.com:5000](http://miniflux.ganesshkumar.com:5000)和[http://blog.ganesshkumar.com:4000](http://blog.ganesshkumar.com:4000)，使得 URL 看起来很难看。

搜索了一段时间，发现使用 nginx 的反向代理可以派上用场。此时，我还想对应用程序实施 SSL，因为我的应用程序需要登录。[让我们加密](https://letsencrypt.org/)提供免费的 SSL 证书。现在我需要把 nginx、证书和我的应用程序放在一起工作。为了减少设置过程，我选择使用 Docker。在这篇文章中，让我们来看看我是如何把所有的碎片放在一起的。

我想在 miniflux 中托管的第一个应用程序是一个极简的阅读器。

让我们在主目录中创建两个文件夹。`data`保存将安装在 docker 中的所有卷，`docker-compose`保存 docker-compose 文件以启动服务和应用程序。

```
$ mkdir data docker-compose 
```

### 1。让我们开始第一个应用程序

为数据和 docker-compose 创建文件夹

```
$ mkdir data/miniflux docker-compose/miniflux 
```

然后在 docker-compose/miniflux 文件夹下创建 *docker-compose.yml* 文件，内容如下

```
version: '3'
services:
  miniflux:
    image: miniflux/miniflux:latest
    ports:
      - "8000:8080"
    depends_on:
      - db
    environment:
      - DATABASE_URL=postgres://<username>:<password>@db/miniflux?sslmode=disable
  db:
    image: postgres:10.1
    environment:
      - POSTGRES_USER=<username>
      - POSTGRES_PASSWORD=<password>
    volumes:
      - ${HOME}/data/miniflux:/var/lib/postgresql/data 
```

现在运行 docker-compose 文件夹中的`docker-compose up -d`将在后台运行这个应用程序。我们可以通过 *localhost:8000* 到达服务器。我们的第一个应用程序现在已经启动并运行。

### 2。正在启动 nginx

为数据和 docker-compose 创建文件夹

```
$ mkdir data/nginx docker-compose/nginx 
```

我们将在 nginx 容器中安装 SSL 证书，这样我们的应用程序将在一个安全的层上提供服务。创建保存证书的文件夹

```
$ mkdir -p data/letsencrypt/certs data/letsencrypt/certs-data 
```

然后创建 *docker-compose.yml* 文件，内容如下

```
version: '3'
services:
  nginx:
    image: nginx:alpine
    container_name: nginx
    restart: always
    network_mode: host
    volumes:
      - ${HOME}/data/letsencrypt/certs:/etc/letsencrypt
      - ${HOME}/data/letsencrypt/certs-data:/data/letsencrypt
      - ${HOME}/data/nginx:/etc/nginx
    ports:
      - 80:80
      - 443:443 
```

在字符串 ngix 容器之前，我们必须为 nginx 创建一个配置文件。用以下内容创建`data/nginx/nginx.conf`文件

```
events {
}

http {
    server {
        listen      80;
        listen [::]:80;
        server_name miniflux.ganesshkumar.com;

         location / {
                rewrite ^ https://$host$request_uri? permanent;
        }

        location ^~ /.well-known {
                allow all;
                root  /data/letsencrypt/;
        }
    }
} 
```

该配置规定将任何对`miniflux.ganesshkumar.com`的请求重定向到`https://ganesshkumar.com`。还奉上了对*的请求。知名的*将从 */data/letscrypt* 目录中提供。这个。Let's Encrypt 将使用众所周知的端点来验证 miniflux 的 DNS 条目。

现在，我们可以从 docker-compose 目录运行`docker-compose up -d`来启动 nginx 服务器。对 miniflux 的请求还不会到达应用程序，因为我们还没有将它路由到 localhost:8000，但是 nginx 服务器仍然会帮助为 miniflux 域设置 SSL 证书。

### 3。设置 SSL 证书

让我们加密提供给工具 certbot 来创建我们的 SSL 证书。让我们使用 certbot 的 docker 图像来实现这一点。运行以下 docker 命令。

```
$ docker run -it --rm \
        -v certs:/etc/letsencrypt \
        -v certs-data:/data/letsencrypt \
        deliverous/certbot \
        certonly \
        --webroot --webroot-path=/data/letsencrypt
        -d miniflux.ganesshkumar.com 
```

上面的命令将生成一个挑战，并试图在`miniflux.ganesshkumar.com/.well-known`解决它。我们的 nginx 服务器有足够的配置来承载。certbot 生成的知名文件夹。

成功执行上述命令后，您将在 data/letsencrypt/certs-data 目录下生成您的证书。

### 4。将请求路由到应用程序

现在让我们修改 *nginx.conf* 文件的内容，将对 miniflux.ganesshkumar.com 的请求路由到 localhost:8000。

将以下代码添加到 *nginx.conf*
的 http 部分

```
 server {
        listen      443           ssl http2;
        listen [::]:443           ssl http2;
        server_name               miniflux.ganesshkumar.com;

        ssl                       on;

        add_header                Strict-Transport-Security "max-age=31536000" always;

        ssl_session_cache         shared:SSL:20m;
        ssl_session_timeout       10m;

        ssl_protocols             TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
        ssl_ciphers               "ECDH+AESGCM:ECDH+AES256:ECDH+AES128:!ADH:!AECDH:!MD5;";

        ssl_stapling              on;
        ssl_stapling_verify       on;
        resolver                  8.8.8.8 8.8.4.4;

        ssl_certificate           /etc/letsencrypt/live/miniflux.ganesshkumar.com/fullchain.pem;
        ssl_certificate_key       /etc/letsencrypt/live/miniflux.ganesshkumar.com/privkey.pem;
        ssl_trusted_certificate   /etc/letsencrypt/live/miniflux.ganesshkumar.com/chain.pem;

        access_log                /dev/stdout;
        error_log                 /dev/stderr info;

        location / {
            proxy_pass           http://127.0.0.1:8000;
            proxy_set_header     Host $host;
            proxy_set_header     X-Forwarded-For $remote_addr;
        }
    } 
```

我们差不多完成了。重启 nginx 服务器以使用最新的配置。在 nginx 的 docker-compose 目录中运行以下命令。

```
$ docker-compose down
$ docker-compose up -d 
```

仅此而已。我们对[http://miniflux.ganesshkumar.com](http://miniflux.ganesshkumar.com)的请求将被重定向到[https://miniflux.ganesshkumar.com](https://miniflux.ganesshkumar.com)，它将由 localhost:8000 提供服务，确保所有请求都通过 SSL 提供服务。

**要添加第二个应用程序**，只需重复所有四个步骤。您将使用 nginx 反向代理从同一台机器上为这两个应用程序提供服务。

### 5。续订 SSL 证书

几个月后，您的 SSL 证书将到期。您可以运行以下命令来续订证书。

```
$ docker run -t --rm \
      -v certs:/etc/letsencrypt \
      -v certs-data:/data/letsencrypt \
      deliverous/certbot \
      renew \
      --webroot --webroot-path=/data/letsencrypt

$ docker-compose kill -s HUP nginx 
```

这种设置简化了向服务器部署任何新应用程序的过程:)