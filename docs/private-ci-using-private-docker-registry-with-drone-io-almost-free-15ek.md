# 通过 Drone.io 使用私有 docker 注册表的私有 CI(几乎免费)

> 原文：<https://dev.to/fuksito/private-ci-using-private-docker-registry-with-drone-io-almost-free-15ek>

在以前的帖子中，我已经描述了如何为你的秘密项目建立你自己的私人 docker 注册表，现在我们想将 CI 添加到这个设置中。

目前，我在一个 5 美元的 VPS 上托管两者，这是一个不错的价格，考虑到我需要为相同数量的私人 docker 图像和私人 TravisCI 支付 DockerHub 的费用，后者每月 1 份兼职工作的费用为 69 美元，他们宣传为“爱好项目的理想选择”，当然不是 69 美元。

# 先决条件

假设您有一个域名`example.com`，并希望在`drone.example.com`上拥有 CI，在`registry.example.com`上拥有私有注册中心

# 设置无人机

Drone 要求您使用一些外部认证提供者，这实际上很方便，我会使用 GitHub 作为认证提供者。同样，这样无人机将导入我在 GitHub 上的私人储存库。

如果你也想使用 GitHub 的话，你应该查阅官方文件，看看如何获得所需的证书:
[https://docs.drone.io/installation/github/single-machine/](https://docs.drone.io/installation/github/single-machine/)

官方文档只展示了如何用`docker run`启动 Drone 的例子，但我更喜欢把它放在`docker-compose.yml`文件中，这样更容易启动/重启。

因此，合并后的`docker-compose.yml`，包括无人机和私有 docker 注册表，看起来应该是:

```
version: "3"
services:
  drone:
    image: drone/drone
    ports:
      - "1080:80"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/drone:/data
    environment:
      - DRONE_USER_FILTER=your_user
      - DRONE_GITHUB_SERVER=https://github.com
      - DRONE_GITHUB_CLIENT_ID=<your id>
      - DRONE_GITHUB_CLIENT_SECRET=<your secret>
      - DRONE_RUNNER_CAPACITY=2
      - DRONE_SERVER_HOST=drone.example.com
      - DRONE_SERVER_PROTO=http
      - DRONE_TLS_AUTOCERT=true
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
      - /root/docker-volumes/auth:/auth 
```

注意重要的环境变量:
`DRONE_USER_FILTER` -只有这个用户能够使用你的无人机，你应该把它限制在你的用户范围内，这样其他人就不能使用你的无人机了。
`DRONE_SERVER_HOST` -您用来创建 OAuth 认证的域

# 运行规范

为了让无人机为你的回购运行规范，你需要添加特殊文件到你的项目`.drone.yml`

```
kind: pipeline
name: default

steps:
- name: test
  image: registry.example.com/rails_api
  commands:
  - bundle exec rspec
  environment:
    RAILS_ENV: 'test'
    DATABASE_CLEANER_ALLOW_REMOTE_DATABASE_URL: 'true'
    REDIS_URL: redis://redis:6379/
    DATABASE_URL: postgres://postgres:@db/ourcodestyle_test
services:
- name: db
  image: 'postgres:11.1-alpine'
  ports:
  - 5432
  environment:
    POSTGRES_USER: postgres
    POSTGRES_DB: rails_api_test
- name: redis
  image: 'redis:3.2-alpine'
  ports:
  - 6379

image_pull_secrets:
- dockerconfigjson 
```

这个文件中有几个有趣的部分:

通过这种方式，您可以指定您希望构建从哪个私有 docker 映像开始。我有这个已经安装了所有 ruby gems 的图像，所以它极大地减少了测试开始运行之前的时间。

另请注意`services`部分。Drone 配置模仿 docker compose 语法，因此很容易配置。

最重要的配置行是最后一行:

```
image_pull_secrets:
- dockerconfigjson 
```

而且在官方文件中也没有很好的描述。该设置说明了 Drone 应该如何为您的私有存储库获取凭证。你需要在运行 Drone 的机器上有一个文件`/root/.docker/config.json`。我没有这个文件。你可以自己创建这个文件，或者有更简单的方法，运行:

```
docker login registry.example.com -u testuser 
```

在运行无人机的机器上，它会创建那个文件。

# 域为无人机

为了共享同一个服务器，你的无人机应该有它自己的子域，而且方便美观。

可以这样添加 nginx 配置:

```
upstream drone {
  server 127.0.0.1:1080;
}

server {
    server_name drone.example.com;

    access_log /var/log/nginx/drone.example.com.access.log combined;
    error_log  /var/log/nginx/drone.example.com.error.log crit;

    server_name_in_redirect off;

    location / {
      proxy_pass                          http://drone;
      proxy_set_header  Host              $http_host;   # required for docker client's sake
      proxy_set_header  X-Real-IP         $remote_addr; # pass on real client's IP
      proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
      proxy_set_header  X-Forwarded-Proto $scheme;
      proxy_read_timeout                  900;
    }

    listen 80;
} 
```

然后不要忘记运行 CertBot 以获得`https`

```
certbot --nginx -d drone.example.com 
```