# 如何为你的项目设置私人 docker 注册表以节省资金

> 原文：<https://dev.to/fuksito/how-to-setup-private-docker-registry-for-your-projects-to-save-money-1ed7>

我不得不为我的非开源项目使用 docker，其中一些是我还不想发布的爱好项目或试用项目，所以我需要私下存储它们。

DockerHub 只允许在免费计划中存储 1 张私人图像，5 张 7 美元，10 张 12 美元，这对于生产项目来说是不错的价格，但对于个人爱好项目来说可能很多。

我在 DigitalOcean 上有一个 5 美元的服务器，用于个人需求，我还想把它用作 docker 注册表和我私人项目的 CI。

本指南中的步骤不是唯一可能的，有许多方法可以达到相同的结果，但我试图使它尽可能快和简单，我将描述每个配置为什么需要它。

# 先决条件

1.  我用的是 Ubuntu 18.04，所以所有命令都会和这个系统有关。

2.  你需要在你的主机上安装 Docker 和 Docker Compose 官方文档:
    [https://docs.docker.com/install/linux/docker-ce/ubuntu/](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

3.  你需要安装一个 **nginx** 。

4.  示例假设您有一个域:`example.com`，并且您希望在:`registry.example.com`上启动注册

# 第一步:设置 nginx

我不使用 nginx 作为 docker 容器，因为我在同一台机器上还有其他一些静态站点，我不想把它们打包到容器中。我还发现在容器外部和主机上使用 nginx 更容易。

ssh 到主机并安装 nginx，如果你还没有的话

```
apt install nginx 
```

然后为您的域创建 nginx 配置:

`/etc/nginx/sites-enabled/registry.example.com`

```
upstream docker-registry {
  server 127.0.0.1:5000;
}

server {
    server_name registry.example.com;

    server_name_in_redirect off;

    # disable any limits to avoid HTTP 413 for large image uploads
    client_max_body_size 0;

    # required to avoid HTTP 411: see Issue #1486 (https://github.com/moby/moby/issues/1486)
    chunked_transfer_encoding on;

    location /v2/ {
      # Do not allow connections from docker 1.5 and earlier
      # docker pre-1.6.0 did not properly set the user agent on ping, catch "Go *" user agents
      if ($http_user_agent ~ "^(docker\/1\.(3|4|5(?!\.[0-9]-dev))|Go ).*$" ) {
        return 404;
      }

      proxy_pass                          http://docker-registry;
      proxy_set_header  Host              $http_host;   # required for docker client's sake
      proxy_set_header  X-Real-IP         $remote_addr; # pass on real client's IP
      proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
      proxy_set_header  X-Forwarded-Proto $scheme;
      proxy_read_timeout                  900;
    }
} 
```

因为我们将使用 BasicAuth 来认证注册表，所以我们需要设置 SSL，这样我们的登录名/密码就不会被中途攻击的人窃取。目前这个 nginx 配置不支持 SSL，而 SSL 是从外部访问我们的注册表所必需的。
我们将使用 CertBot 来更新我们的 nginx 配置，以支持 SSL。

# 步骤 2:确定机器人

安装 CertBot，如下所述:[https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx](https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx)

现在你可以运行命令:

```
certbot --nginx -d registry.example.com 
```

它将修改`/etc/nginx/sites-enabled/registry.example.com`以便支持 SSL。

# 步骤 3:坞站注册

官方文档在这里:[https://docs.docker.com/registry/deploying/](https://docs.docker.com/registry/deploying/)
官方文档很长，描述了许多不同用例的选项

只需通过 ssh 连接到您的主机，转到/root 文件夹。

```
mkdir docker-volumes
mkdir docker-volumes/registry/
mkdir docker-volumes/registry/registry
mkdir docker-volumes/registry/auth 
```

在那里创建这样的`docker-compose.yml`文件:

```
version: "3"
services:
  registry:
    restart: always
    image: 'registry:2'
    ports:
      - "5000:5000"
    environment:
      REGISTRY_AUTH: htpasswd
      REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
      REGISTRY_AUTH_HTPASSWD_REALM: Registry Realm
    volumes:
      - /root/docker-volumes/registry/registry:/var/lib/registry
      - /root/docker-volumes/registry/auth:/auth 
```

# 第四步:认证

然后，我们需要通过登录/密码限制对注册表的访问，我们将使用 BasicAuth。为此，我们需要生成 htpasswd 文件，并将其放入注册表容器中。将 testuser/testpassword 更改为您自己的。

```
docker run --entrypoint htpasswd registry:2 -Bbn testuser testpassword > ./docker-volumes/registry/auth/htpasswd 
```

# 第五步:启动这一切

现在我们可以启动注册表，运行:

```
docker-compose up -d 
```

同样重启 nginx，这样它就可以加载 registry.example.com 的新配置了

```
 /etc/init.d/nginx restart 
```

# 第六步:验证

为了检查我们是否正确地配置了所有的东西，请在您的本地机器(可能是您的笔记本电脑)上打开控制台，尝试登录:

```
docker login registry.example.com 
```

使用步骤 4 中的凭据

现在应该都涨了。在接下来的帖子中，我将写如何使用 Drone.io 设置您的私有 CI，以便它可以在其构建中使用您的私有注册表。