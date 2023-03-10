# 使用 docker 部署 python-django 应用程序

> 原文：<https://dev.to/lewiskori/deploying-a-python-django-application-using-docker-3d09>

这篇文章最初发表在我的个人网站上

嘿，我写这篇文章的灵感来自于我尝试将我的部署转移到使用 docker 的经历，特别是对于 django 应用程序，但是我找不到一个全面的地方/文章来满足我的需要。希望这篇文章能帮助那些和我一样感到困惑的人。

你们中的很多人可能已经听说过这句话，无论你走到哪里，这句话都会被抛向四周。你可能在谷歌上搜索过 docker 这个术语，甚至尝试过使用它，但是可能已经放弃了。见鬼，说实话，在我完全投入之前，我确实坚持了一两次。一开始可能会有点吓人，但是哦，天哪！一旦你开始使用 docker，就没有回头路了。从生产环境转移到开发环境的容易程度至少可以说是令人兴奋的！！
这么多废话，我们开始吧。

## docker 是什么？

Docker 是一个开源工具，可以在软件容器中自动部署应用程序。这类似于虚拟机，只不过更易移植，更资源友好，并且更依赖于主机操作系统。关于 docker 工作的详细信息，我推荐阅读[这篇文章](https://dev.to/djangostars/what-is-docker-and-how-to-use-it-with-python-tutorial-87a)，对于那些不喜欢阅读长文的人来说，youtube 上的这个[系列教程在向我介绍 docker 的概念时特别有用。](https://www.youtube.com/playlist?list=PLhW3qG5bs-L99pQsZ74f-LC-tOEsBp2rK)

## 安装对接器。

如果您的机器上没有安装 docker，请根据您的操作系统遵循以下详细步骤。展望未来，我假设您已经有了一个现有的 django 应用程序，所以本教程只是指导您如何将其容器化。

1. [windows 10 pro](https://docs.docker.com/docker-for-windows/install/)
2。 [windows 10 那不是 pro](https://docs.docker.com/toolbox/toolbox_install_windows/)T5】3。 [ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04)

## 入门

为了部署一个典型的 django 应用程序，您需要以下服务来运行它。

1.  nginx——服务静态文件和网络服务器
2.  postgres/您选择的任何数据库
3.  安装了 gunicorn 的 python

要启动这些服务，您需要一个 dockerfile 文件。这基本上是一个文本文档，突出显示了 cli 上的所有命令以及组装映像通常需要采取的步骤。

### 1。Python 图像

```
FROM python:3.6

RUN mkdir /code
WORKDIR /code

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . . 
```

Enter fullscreen mode Exit fullscreen mode

1.  第一行必须从关键字的**开始。它告诉 docker，你希望你的图片基于哪个基础图片。在这种情况下，我们从 python 3.6 映像创建一个映像。**

2.  第二行是命令 **RUN** 用于运行镜像上的指令，在本例中我们正在创建一个名为**T3 的目录代码。** 在此之后 ***WORKDIR*** 将代码目录设置为工作目录，这样 dockerfile 上的任何进一步的指令都出现在这个目录中。

3.  ***复制*** 命令将特定文件从主机复制到我们正在创建的映像中。requirements.txt 文件将被复制到之前设置的工作目录中。之后，运行 pip install 命令来安装项目所需的 python 包。

4.  最后，将您当前工作目录的项目文件从主机复制到 docker 映像上。

为了构建这个映像，运行简单的命令

`docker build .`

放在当前 dockerfile 位置目录下。
在我们的用例中，我们将有多个图像，为每个图像运行这个命令将会令人厌烦。因此需要 docker-compose，在我们完成时会有更多的介绍。

### 2。Nginx 图像

```
FROM nginx

RUN rm /etc/nginx/conf.d/default.conf
COPY mysite.conf /etc/nginx/conf.d 
```

Enter fullscreen mode Exit fullscreen mode

这些命令与 python 相同，只是特定于 nginx
在这种情况下，我们使用 nginx 基本映像，删除 nginx 附带的默认配置文件，并用我们的自定义配置文件替换它。
可能是这样的

```
upstream my_site {
    server web:8080;
}

server {

    listen 80;
    charset utf-8;
    server_name  127.0.0.1;

    client_max_body_size 4G;
    access_log /code/logs/nginx-access.log;
    error_log /code/logs/nginx-error.log;

    location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_redirect off;
        if (!-f $request_filename) {
            proxy_pass http://my_site;
            break;
        }
    }

    location /static/ {
        autoindex on;
        alias /code/static_cdn/;
    }

    location /media/ {
        autoindex on;
        alias /code/media_cdn/;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

文件位置当然与您自己的配置相关。

### 3。postgres

最后是数据库，在这个用例中，我使用了 postgres。

```
FROM postgres:latest

COPY ./init/01-db_setup.sh /docker-entrypoint-initdb.d/01-db-setup.sh 
```

Enter fullscreen mode Exit fullscreen mode

现在你在想
***“但是 Lewis，这个 init 文件是什么？”***
关于上下文，让我们看看我们项目中的 postgres 目录

posters
【posters/dock file】
posters/init
posters/init/01-db _ setup . sh

这是一个 shell 脚本(docker 入口点),指定在数据库容器上运行什么命令，比如创建数据库、用户以及向所述用户授予特权。

```
#!/bin/sh

psql -U postgres -c "CREATE USER $POSTGRES_USER PASSWORD '$POSTGRES_PASSWORD'"
psql -U postgres -c "CREATE DATABASE $POSTGRES_DB OWNER $POSTGRES_USER"
psql -U postgres -c "GRANT ALL PRIVILEGES ON DATABASE $POSTGRES_DB TO $POSTGRES_USER" 
```

Enter fullscreen mode Exit fullscreen mode

注意:当你创建这个文件时，不要忘记通过运行
使它可执行

`sudo chmod u+x filename.sh`

### 4。用 docker-compose 包装东西

此时，你可能已经注意到我们有很多 dockerfiles，
使用 docker-compose，我们可以使用
命令
方便地构建所有这些图像

`docker-compose build .`

首先，我们需要在项目目录中创建一个 docker-compose.yml 文件。我们将在这个文件中指定 webapp 运行所需的服务。

```
version: '3'
services: 

  web:

      build: .
      container_name: great
      volumes: 
      - .:/code
      - static:/code/static_cdn
      - media:/code/media_cdn
      depends_on: 
          - postgres
      expose: 
        - 8080
      command: bash -c "python manage.py collectstatic --no-input && python manage.py makemigrations && python manage.py migrate && gunicorn --workers=3 projectname.wsgi -b 0.0.0.0:8080"

  postgres:
      build: ./postgres
      restart: unless-stopped
      expose:
        - "5432"
      environment:   # will be used by the init script
            LC_ALL: C.UTF-8
            POSTGRES_USER: myuser
            POSTGRES_PASSWORD: mypassowrd.
            POSTGRES_DB: mydb
      volumes:
          - pgdata:/var/lib/postgresql/data/  

  nginx:
      restart: always
      build: ./nginx/
      volumes: 
        - ./nginx/:/etc/nginx/conf.d
        - ./logs/:/code/logs
        - static:/code/static_cdn
        - media:/code/media_cdn
      ports: 
        - "1221:80"
      links:
        - web  
volumes: 
  pgdata:
  media:
  static: 
```

Enter fullscreen mode Exit fullscreen mode

一行一行地浏览这个命令:

1.  version -指定我们将使用的 docker-compose 的语法版本
2.  服务-从现在开始，我们将重点介绍我们将要推出的不同服务。如上所述，这些将是 nginx、python 和 postgres，并按照我们的要求命名。我把它们命名为 nginx、web 和 postgres。
3.  还记得我们花时间写的那些 docker 文件吗？很好。使用 build 命令，您可以指定每个 dockerfile 文件的位置，并且基于这些文件上的命令，将构建一个映像。
4.  container _ name——这将为容器提供您指定的名称，一旦容器启动并运行。
5.  卷——这是 docker 容器和主机之间共享数据的一种方式。它们还允许数据持久化，即使在 docker-containers 被销毁并重新创建后也是如此，因为你会发现自己经常这样做。
6.  端口——这是为了指定 docker 容器中的哪些端口被映射到主机，以 nginx 服务为例，容器的端口 80 被映射到主机的端口 1221。
7.  expose——公开端口使链接的服务可以访问端口，但不能从主机访问。
8.  restart -指定容器在意外关闭时的行为
9.  命令——指示容器在启动之前运行哪些命令，在本例中，web 服务中的链接命令用于检查数据库中的更改，并将 web 服务绑定到端口 8080。

### 5。最后的步骤

为了构建图像，现在只需运行

`docker-compose build`

这可能需要几分钟来构建，因为基础映像正在下载，以防您本地没有它们，

要启动各种服务容器，只需运行

`docker-compose up`

或者如果您想要指定在一个目录中有多个 docker-compose 文件的情况下运行哪个 compose 文件

`docker-compose -f filename.yml up`

***免责声明*** :不要忘记在 django 的 settings.py 文件中设置 Debug = False 和 allowed hosts，以反映您将使用的域名或 ip 地址。
除此之外，更改 django 附带的默认 sqlite3 数据库，以反映我们在 postgres 服务的环境部分指定的数据库和用户名
，就像这样

```
DATABASES = {
'default': {
'ENGINE': 'django.db.backends.postgresql_psycopg2',
'NAME': 'mydb',
'USER': 'myuser',
'PASSWORD': 'mypassword',
'HOST': 'postgres',
'PORT': 5432,
}
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。
要查看正在运行的站点，运行

1.  本地主机:1221
2.  虚拟机器 ip:1221(对于使用 docker-toolbox 的用户)

如果你想停止集装箱

`docker-compose stop`

启动停止的容器

`docker-compose start`

销毁集装箱

`docker-compose down`

您对 docker 文件进行了更改，并且需要应用这些更改

`docker-compose down && docker-compose build && docker-compose up`

现在，要让站点在 web 上运行，只需在 web 服务器上为本地机器 nginx(或 apache)创建一个配置文件，并简单地将它指向运行 django 应用程序的 docker-container。在这种情况下，您将把它指向 nginx 容器。1221 年

要获得 docker 需要的常用命令列表，请阅读这篇[简明帖子](https://dev.to/aduranil/10-docker-compose-and-docker-commands-that-are-useful-for-active-development-22f9)

对于 laravel 开发人员来说，这里有一些可以帮助你开始使用 docker

非常感谢您的时间，我希望这篇文章是有用的。如果你想要更多，请随时[联系我](https://lewiskori.com/#contact/)

别忘了点赞，分享[订阅](https://mailchi.mp/c42286076bd8/lewiskori)。如果你有更多要补充的，请在下面留言。

谢了。