# 在系统上没有 ruby/rails 的情况下建立一个新的 Rails 项目。

> 原文：<https://dev.to/lornatumuhairwe/setting-up-a-new-rails-project-without-having-ruby-rails-on-your-system-3ge7>

网上有很多关于*用 docker* 设置 rails 应用的文章。你可能会想，“为什么要再写一个？”。我也问过自己同样的问题。我这么做的原因是，当我学会如何使用 Docker 时，我试图在其中安装一个 rails 应用程序。我接触到的大多数资源都在使用 Docker Compose。这对我练习 docker 没有太大帮助，因为我希望能够编写那些长的 Docker 命令，并准确地遵循那个`docker-compose.yml`文件中发生的事情。我写这篇文章是为了我自己和任何有同样需求的人。万一你在探索中遇到困难，你可以从这里得到一两分钱。让我们开始吧！

1.  创建你将要工作的目录，然后`cd`进入
    目录。我打算把这个应用程序命名为`dockerapp`。

    ```
    mkdir dockerapp && cd $_ 
    ```

2.  然后创建初始文件。

    ```
    touch {Gemfile,Dockerfile} 
    ```

3.  在`Gemfile`中，放入下面的代码。

    ```
    source 'https://rubygems.org'

    gem 'rails', '~> 5.2' 
    ```

4.  在`Dockerfile`

    ```
    FROM ruby:2.6-alpine

    RUN apk update -qq && apk add --update alpine-sdk postgresql-dev nodejs yarn tzdata

    RUN mkdir /app WORKDIR /app

    COPY Gemfile Gemfile

    RUN bundle install

    COPY . .

    LABEL maintainer="Lorna Tumuhairwe <lornatumuhairwe@gmail.com>" version="1.0"

    EXPOSE 3000

    CMD rails s --port=3000 -b='0.0.0.0' 
    ```

5.  建立形象。

    ```
    docker image build -t dockerapp . 
    ```

6.  现在您的映像已经安装了 ruby 和 rails。所以我们现在使用
    已安装的 rails 来生成一个新的 rails 项目。

    ```
    docker container run -it -v ${PWD}:/app dockerapp rails new . --database=postgresql -f -T 
    ```

7.  此时，你应该在文件夹中有 rails 应用程序，因为你已经用`-v ${PWD}:/app`挂载了这个卷。更新
    `config/database.yml`文件以包含下面的代码。我已经对`hostname`、`username`和`database`的名称做了
    更改，以匹配
    我们将在运行 postgresql
    容器时使用的 postgres 名称和应用程序名称。

    ```
    default: &default
      adapter: postgresql
      encoding: unicode
      host: postgres
      username: postgres
      pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>

    development:
      <<: *default
      database: dockerapp_development

    test:
      <<: *default
      database: dockerapp_test

    production:
      <<: *default
      database: dockerapp_production
      username: app
      password: <%= ENV['APP_DATABASE_PASSWORD'] %> 
    ```

8.  由于 Gemfile 已经更改，请重建 docker 映像。

    ```
    docker image build -t dockerapp . 
    ```

9.  创建一个网络，用于连接 app 容器和
    postgres 容器。

    ```
    docker network create dockerapp-network 
    ```

10.  使用下面的命令运行 postgresql 容器。即使你还没有下载它，它也会被下载和使用。

    ```
     docker container run -it --rm -v pgdata:/var/lib/postgresql/data --name postgres --net dockerapp-network -p 5432:5432 postgres:9.6-alpine 
    ```

11.  在同一网络中运行应用程序。

    ```
     docker container run --rm -it -v ${PWD}:/app -p 3000:3000 --name dockerapp --net dockerapp-network dockerapp 
    ```

12.  打开另一个终端并创建数据库。

    ```
     docker exec dockerapp rails db:create 
    ```

13.  在你的浏览器访问`localhost:3000`时，你应该在 rails
    的欢迎页面上！

您可能已经注意到，您总是必须运行两个长命令来运行您的应用程序(步骤 10 和 11)。但是要在一个命令中完成所有这些，您可以使用 Docker compose。

#### 码头工人缀来救援。

创建 dock-compose . yml 文件。

```
version: '3'
services:
  postgres:
    image: postgres:9.6-alpine
    volumes:
      - ./tmp/db:/var/lib/postgresql/data
  dockerapp:
    build: .
    command: bundle exec rails s -p 3000 -b '0.0.0.0'
    volumes:
      - .:/app
    ports:
      - "3000:3000"
    depends_on:
      - postgres 
```

按照上面的步骤，确保你的容器都停止了。通过跑步，

```
 docker container stop dockerapp postgres 
```

在同一个目录中，运行您的应用程序

```
 docker-compose up 
```

这将同时运行 postgresql 和应用程序映像。如果您在 dockerapp 容器退出时遇到任何问题，请确保您在`temp/pids/server.pid`中没有 server.pid 文件。这是一个 rails 特有的问题，可以通过删除文件来解决。或者你可以写一个脚本，当应用程序启动时，如果它存在，总是删除它。这在 [docker 文档](https://docs.docker.com/compose/rails/)中有很好的记录。

感谢您的阅读！本文概述的步骤仅在 MacOS 上测试。

简单地说，这是我在这个平台上的第一个帖子，我在这里学到了很多，我也很高兴分享我所学到的。希望对某个人有帮助。❤️