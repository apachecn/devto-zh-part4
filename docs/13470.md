# 使用 Terraform 和 AWS Fargate 将容器部署到生产中——集装箱化

> 原文：<https://dev.to/ianrodrigues/deploying-containers-to-production-with-terraform-and-aws-fargate-containerization-2eb5>

在[的前一篇文章](https://dev.to/ianrodrigues/deploying-containers-to-production-with-terraform-and-aws-fargate-introduction-28l8)中，我已经介绍了一些[可以在 AWS](https://aws.amazon.com/ecs/) 上运行容器的服务。此外，我还展示了 [Terraform](https://terraform.io) ，这是一个使用代码来声明和提供这些服务的工具。这一次，我将使用几乎相同的配置，在本地和生产环境中设置项目在 [Docker](https://docker.io) 上运行。

尽管我对 [PHP](https://php.net) 有更多的经验，但我将提供一个用 Python ( [Django](https://www.djangoproject.com/) )编写的 [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer) ，因为我最近经常使用它。请不要把重点放在应用本身；只是为了这个帖子而写的。此外，我相信您可以将我在这里使用的原则应用于任何语言或框架。*那么，让我们动手吧！*

## 设置本地环境

首先，让我们设置本地环境来运行 Docker。这样，我们将最终拥有一个与生产环境完全相同的工作环境。为此，我们将使用 [Docker 编写](https://docs.docker.com/compose/)。在根目录下创建一个名为`docker-compose.yml`的新文件:

```
version: '3.7'

volumes:
  postgres-data:
    driver: local

services:
  django:
    build: .
    volumes:
      - .:/usr/local/app
    ports:
      - 8000:8000
    depends_on:
      - database

  database:
    image: postgres:11-alpine
    volumes:
      - postgres-data:/var/lib/postgresql
    environment:
      POSTGRES_USER: wannajob
      POSTGRES_PASSWORD: wannajob
      POSTGRES_DB: wannajob 
```

Enter fullscreen mode Exit fullscreen mode

我不会进入`docker-compose.yml`文件的细节。如果你想了解更多，[看看这个](https://docs.docker.com/compose/compose-file/)。现在让我们创建一个`Dockerfile`。Docker Compose 将使用它来构建 Django 的容器:

```
FROM python:3.6-slim-stretch

ENV PYTHONUNBUFFERED 1

WORKDIR /usr/local/app

RUN pip install --upgrade pip safety bandit pylint flake8 pytest coverage

COPY requirements.txt /usr/local/app/requirements.txt
RUN pip install -r requirements.txt

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"] 
```

Enter fullscreen mode Exit fullscreen mode

再说一遍，我不会深入讨论`Dockerfile`的细节。如果你想了解更多，请查看。一切都设置好了，是时候让项目启动并运行了:

```
$ docker-compose up -d 
```

Enter fullscreen mode Exit fullscreen mode

最后，运行迁移:

```
$ docker-compose run --rm --no-deps django python manage.py migrate 
```

Enter fullscreen mode Exit fullscreen mode

看到了吗？该项目已准备好使用 Docker 进行本地开发。现在，在任何地方启动这个项目都很容易。你可以把它分享给你的整个团队，每个人都会有同样的环境。不再是“但是它在我的机器上工作！”。

## 准备生产

现在项目已经在本地运行了，我们可以花一些时间做一些调整来为生产做准备。虽然我们在本地使用`runserver`命令——一个轻量级的开发 web 服务器——来为应用程序提供服务，但在生产中，我们将需要一个“真正的”web 服务器。这就是 Nginx 的用武之地。然而，我们不能直接通过 Nginx 提供应用程序，因为它不能处理 Python。我们需要一些东西(一个 [WSGI](https://en.wikipedia.org/wiki/Web_Server_Gateway_Interface) )让 Nginx 转发请求，处理 Python 代码，发送回 Nginx，然后将响应返回给客户端。为此，我们将使用 [Gunicorn](https://gunicorn.org/) 。

首先要做的是更新`Dockerfile`。我们将利用 [Docker 多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)，参见:

```
##############
# Base Stage
##############
FROM python:3.6-slim-stretch AS base

ENV PYTHONUNBUFFERED 1

WORKDIR /usr/local/app

RUN pip install --upgrade pip gunicorn

COPY requirements.txt /usr/local/app/requirements.txt
RUN pip install -r requirements.txt

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "--log-level", "warning", "--reload", "wannajob.wsgi:application"]

#####################
# Development Stage
#####################
FROM base AS development

RUN pip install --upgrade safety bandit pylint flake8 pytest coverage

####################
# Production Stage
####################
FROM base AS production

ENV DJANGO_SETTINGS_MODULE 'wannajob.settings.production'

COPY . /usr/local/app

#################
# Statics Stage
#################
FROM production AS statics

RUN python manage.py collectstatic --no-input --clear

############################
# Production (Nginx) Stage
############################
FROM nginx:1.17-alpine AS nginx

COPY ./docker/nginx/django.conf /etc/nginx/conf.d/default.conf

WORKDIR /usr/local/app

RUN mkdir statics
COPY --chown=nginx:nginx --from=statics /usr/local/app/static /usr/local/app/static 
```

Enter fullscreen mode Exit fullscreen mode

然后，创建`docker/nginx/django.conf`文件:

```
server {
  listen 80;

  location = /favicon.ico {
    access_log    off;
    log_not_found off;
  }

  location /static/ {
    alias /usr/local/app/static/;
  }

  location / {
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_pass http://django:8000;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，更新`docker-compose.yml`，使用 Nginx:

```
version: '3.7'

volumes:
  postgres-data:
    driver: local

services:
  django:
    build:
      context: .
      target: development
    volumes:
      - .:/usr/local/app
    depends_on:
      - database

  nginx:
    image: nginx:1.17-alpine
    volumes:
      - ./docker/nginx:/etc/nginx/conf.d
      - ./static:/usr/local/app/static
    ports:
      - 8000:80
    depends_on:
      - django

  database:
    image: postgres:11-alpine
    volumes:
      - postgres-data:/var/lib/postgresql
    environment:
      POSTGRES_USER: wannajob
      POSTGRES_PASSWORD: wannajob
      POSTGRES_DB: wannajob 
```

Enter fullscreen mode Exit fullscreen mode

让我们重新构建应用程序:

```
$ docker-compose up -d --build 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个项目通过 Nginx 在本地提供服务，与我们将来在生产中所做的没有什么不同。此外，由于 Docker 多阶段构建，我们在本地拥有了所有必要的开发工具，但我们可以在生产中运行时摆脱它们，这将导致更轻量级的映像。

看看开发和生产图像的大小:

```
$ docker images --filter reference=ianrodrigues/wannawork-app 
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
ianrodrigues/wannawork-app   production          29a4de730436        3 minutes ago       196MB
ianrodrigues/wannawork-app   development         cf3a9bf4ca10        2 hours ago         223MB 
```

Enter fullscreen mode Exit fullscreen mode

很酷吧。！

正如您所看到的，为本地运行的项目设置 Docker 是很简单的，只需做一些修改，您就可以使用相同的设置进行生产。由于 Docker 多阶段构建，您可以创建灵活的 Docker 文件，这将产生更轻量级的图像，非常适合生产。

本文中使用的[源代码](https://github.com/ianrodrigues/wannajob-api)可以在 Github 上获得。如果你有问题或建议，请在评论中告诉我。在下一篇文章中，我将使用 Terraform 在 AWS 上设置环境，敬请关注！