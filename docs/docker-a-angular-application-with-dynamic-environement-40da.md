# 动态环境下的角度应用

> 原文：<https://dev.to/beaussart/docker-a-angular-application-with-dynamic-environement-40da>

正如我们在[我之前的帖子](https://nbe.io/load-a-configuration-at-runtime-with-angular/)中看到的，我们可以在 angular 中进行动态配置。它的一个很大的用途是在 docker 容器中！我们将创建一个多步骤的 docker 构建，以尽可能最小的规模和动态配置。但是首先，我们需要为我们的应用程序创建 nginx 配置。

## 先决条件

*   NodeJS +8
*   角度 CLI ( `npm i -g @angular/cli@latest`或`yarn global add @angular/cli@latest`)
*   我之前的帖子中的[中的语言运行时配置](https://nbe.io/load-a-configuration-at-runtime-with-angular/)
*   Docker +17.05
*   对 Docker 和 Angular CLI 命令有基本了解

## NGINX 配置

我们的第一步将是配置我们的 NGINX 服务器来服务我们的应用程序，并 gzip 它，这样我们就有了更好的性能:

```
# ./docker/nginx/default.conf

server {
  listen 80;

  sendfile on;

  default_type application/octet-stream;

  gzip              on;
  gzip_http_version 1.1;
  gzip_disable      "MSIE [1-6]\.";
  gzip_min_length   1100;
  gzip_vary         on;
  gzip_proxied      expired no-cache no-store private auth;
  gzip_types        text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;
  gzip_comp_level   9;

  root /usr/share/nginx/html;

  location / {
    try_files $uri $uri/ /index.html =404;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了它，我们就可以做码头集装箱了！

## Dockerfile

我们的 docker 文件将有两个阶段。一个阶段将构建我们的应用程序，另一个阶段将构建只运行 NGINX 的应用程序。

这就是我们如何实现的:

```
### STAGE 1: Build ###

# We label our stage as 'builder'
FROM node:9 as builder

COPY package.json yarn.lock ./

## Storing node modules on a separate layer will prevent unnecessary npm installs at each build
RUN yarn install && mkdir /ng-app && mv ./node_modules ./ng-app/

## Move to /ng-app (eq: cd /ng-app)
WORKDIR /ng-app

# Copy everything from host to /ng-app in the container
COPY . .

## Build the angular app in production mode and store the artifacts in dist folder

RUN yarn build --prod --output-path=dist

### STAGE 2: Setup ###

FROM nginx:1.13.3-alpine

## Copy our default nginx config
COPY docker/nginx/default.conf /etc/nginx/conf.d/

## Remove default nginx website
RUN rm -rf /usr/share/nginx/html/*

## From 'builder' stage copy over the artifacts in dist folder to default nginx public folder
COPY --from=builder /ng-app/dist /usr/share/nginx/html

CMD ["nginx", "-g", "daemon off;"] 
```

Enter fullscreen mode Exit fullscreen mode

我们的最后一步将是创建一个 docker-compose 与配置！

## 复合坞站

这里是我们的码头-堆肥。yml:t0]

```
version: '3.1'

services:
  web-app:
    build: .
    ports:
      - 8090:80
    volumes:
      - ./front-config/local.json:/usr/share/nginx/html/assets/config.json 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我构建了一个本地映像，但是您可以使用`image`标签来使用构建映像。

我们找到了！一个完全可配置的带有角度应用的 docker 图像！