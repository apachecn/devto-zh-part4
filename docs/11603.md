# Ruby on Whales:记录 Ruby 和 Rails 的开发

> 原文：<https://dev.to/evilmartians/ruby-on-whales-dockerizing-ruby-and-rails-development-4dm7>

*最初发布于[火星编年史](https://evilmartians.com/chronicles/ruby-on-whales-docker-for-ruby-rails-development)* 。

*这篇文章是我最近的 RailsConf 演讲“改造传统 Rails 应用”([视频](https://www.youtube.com/watch?v=-NKpMn6XSjU)，[幻灯片](https://speakerdeck.com/palkan/railsconf-2019-terraforming-legacy-rails-applications))的 b 面。*

在这篇文章中，我不会说服你转而使用 Docker 进行应用程序开发(尽管你可以查看 RailsConf 视频中的一些论点)。我的目标是分享我目前用于 Rails 项目的配置，它诞生于[邪恶火星人](https://evilmartians.com/)的~~生产~~开发中。放心用吧！

大约三年前，我已经开始在我的开发环境中使用 Docker(而不是对我的 4GB RAM 笔记本电脑来说太重的 vagger)。当然，从一开始就不是一帆风顺的——我花了两年时间试图找到一个足够好的配置，不仅适合我自己，也适合我的团队。

让我在这里展示这个配置，并解释(几乎)它的每一行，因为我们都已经有足够的神秘教程，只是假设你*知道的东西*。

> 源代码可以在 GitHub 的[evil mars/terra forming-rails](https://github.com/evilmartians/terraforming-rails/blob/master/examples/dockerdev)库中找到。

在本例中，我们使用以下堆栈:

*   Ruby 2.6.3
*   PostgreSQL 11
*   NodeJS 11 & Yarn(用于 Webpacker 支持的资产编译)

## [`Dockerfile`T4】](https://github.com/evilmartians/terraforming-rails/blob/master/examples/dockerdev/.dockerdev/Dockerfile)

`Dockerfile`为我们的 Ruby 应用程序定义了*环境*:这是我们运行服务器、控制台(`rails c`)、测试、Rake 任务、以任何方式与我们的代码交互的地方*作为开发人员* :

```
ARG RUBY_VERSION
# See explanation below
FROM ruby:$RUBY_VERSION

ARG PG_MAJOR
ARG NODE_MAJOR
ARG BUNDLER_VERSION
ARG YARN_VERSION

# Add PostgreSQL to sources list
RUN curl -sSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
  && echo 'deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main' $PG_MAJOR > /etc/apt/sources.list.d/pgdg.list

# Add NodeJS to sources list
RUN curl -sL https://deb.nodesource.com/setup_$NODE_MAJOR.x | bash -

# Add Yarn to the sources list
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
  && echo 'deb http://dl.yarnpkg.com/debian/ stable main' > /etc/apt/sources.list.d/yarn.list

# Install dependencies
# We use an external Aptfile for that, stay tuned
COPY .dockerdev/Aptfile /tmp/Aptfile
RUN apt-get update -qq && DEBIAN_FRONTEND=noninteractive apt-get -yq dist-upgrade && \
  DEBIAN_FRONTEND=noninteractive apt-get install -yq --no-install-recommends \
    build-essential \
    postgresql-client-$PG_MAJOR \
    nodejs \
    yarn=$YARN_VERSION-1 \
    $(cat /tmp/Aptfile | xargs) && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    truncate -s 0 /var/log/*log

# Configure bundler and PATH
ENV LANG=C.UTF-8 \
  GEM_HOME=/bundle \
  BUNDLE_JOBS=4 \
  BUNDLE_RETRY=3
ENV BUNDLE_PATH $GEM_HOME
ENV BUNDLE_APP_CONFIG=$BUNDLE_PATH \
  BUNDLE_BIN=$BUNDLE_PATH/bin
ENV PATH /app/bin:$BUNDLE_BIN:$PATH

# Upgrade RubyGems and install required Bundler version
RUN gem update --system && \
    gem install bundler:$BUNDLER_VERSION

# Create a directory for the app code
RUN mkdir -p /app

WORKDIR /app 
```

此配置仅包含基本要素，可用作起点。让我来展示一下我们在做什么。

前两行可能看起来有点奇怪:

```
ARG RUBY_VERSION
FROM ruby:$RUBY_VERSION 
```

为什么不仅仅是`FROM ruby:2.6.3`，或者其他任何当前的 Ruby 稳定版本？我们希望使用 Dockerfile 作为一种模板，从外部配置我们的环境:

*   运行时依赖的确切版本在`docker-compose.yml`中指定(见下文)；
*   可安装的依赖项列表存储在一个单独的文件中(也见下文)。

以下三行定义了 PostgreSQL、NodeJS、Yarn 和 Bundler 版本的参数:

```
ARG PG_MAJOR
ARG NODE_MAJOR
ARG BUNDLER_VERSION
ARG YARN_VERSION 
```

因为我们不希望任何人在没有 [Docker Compose](https://docs.docker.com/compose/) 的情况下使用这个 Docker 文件，所以我们不提供默认值。

通过`apt`安装 PostgreSQL、NodeJS、Yarn 需要将它们的 deb 包 repos 添加到源代码列表中。

对于 PostgreSQL(基于[官方文档](https://www.postgresql.org/download/linux/debian/) ):

```
RUN curl -sSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
  && echo 'deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main' $PG_MAJOR > /etc/apt/sources.list.d/pgdg.list 
```

对于节点 JS(来自[节点源报告](https://github.com/nodesource/distributions/blob/master/README.md#debinstall) ):

```
RUN curl -sL https://deb.nodesource.com/setup_$NODE_MAJOR.x | bash - 
```

供纱(来自[官网](https://yarnpkg.com/en/docs/install#debian-stable) ):

```
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
  && echo 'deb http://dl.yarnpkg.com/debian/ stable main' > /etc/apt/sources.list.d/yarn.list 
```

现在是安装依赖项的时候了，即运行`apt-get install` :

```
COPY .dockerdev/Aptfile /tmp/Aptfile
RUN apt-get update -qq && DEBIAN_FRONTEND=noninteractive apt-get -yq dist-upgrade && \
  DEBIAN_FRONTEND=noninteractive apt-get install -yq --no-install-recommends \
    build-essential \
    postgresql-client-$PG_MAJOR \
    nodejs \
    yarn \
    $(cat /tmp/Aptfile | xargs) && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    truncate -s 0 /var/log/*log 
```

首先，我们来说说 Aptfile 的招数:

```
COPY .dockerdev/Aptfile /tmp/Aptfile
RUN apt-get install \
    $(cat /tmp/Aptfile | xargs) 
```

我从 [heroku-buildpack-apt](https://github.com/heroku/heroku-buildpack-apt) 借用了这个想法，它允许在 heroku 上安装额外的包。如果您使用这个 buildpack，您甚至可以在本地和生产环境中重用同一个 Aptfile(尽管 buildpack 提供了更多的功能)。

我们的[默认 Aptfile](https://github.com/evilmartians/terraforming-rails/blob/master/examples/dockerdev/.dockerdev/Aptfile) 只包含一个包(我们使用 Vim 来编辑 Rails 凭证):

```
vim 
```

在我之前参与的一个项目中，我们使用 LaTeX 和 [TexLive](https://www.tug.org/texlive/) 生成 pdf。我们的 Aptfile 可能是这样的(那几天我没用这一招):

```
vim
texlive
texlive-latex-recommended
texlive-fonts-recommended
texlive-lang-cyrillic 
```

这样，我们将特定于任务的依赖关系保存在一个单独的文件中，使我们的 docker 文件更加通用。

关于`DEBIAN_FRONTEND=noninteractive`，我恳请你看看在 Ask Ubuntu 上[的回答。](https://askubuntu.com/a/972528)

通过不安装推荐的软件包，`--no-install-recommends`开关帮助我们节省了一些空间(并使我们的图像更纤细)。查看更多[此处](http://xubuntugeek.blogspot.com/2012/06/save-disk-space-with-apt-get-option-no.html)。

这个`RUN` ( `apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && truncate -s 0 /var/log/*log`)的最后一部分也是为了同样的目的——清除本地存储库中检索到的包文件(我们安装了所有东西，我们不再需要它们)以及安装过程中创建的所有临时文件和日志。我们需要这个清理在同一个`RUN`语句中，以确保这个特定的 [Docker 层](https://docs.docker.com/storage/storagedriver/#images-and-layers)不包含任何垃圾。

最后一部分主要献给 Bundler:

```
ENV LANG=C.UTF-8 \
  GEM_HOME=/bundle \
  BUNDLE_JOBS=4 \
  BUNDLE_RETRY=3
ENV BUNDLE_PATH $GEM_HOME
ENV BUNDLE_APP_CONFIG=$BUNDLE_PATH \
  BUNDLE_BIN=$BUNDLE_PATH/bin
ENV PATH /app/bin:$BUNDLE_BIN:$PATH

# Upgrade RubyGems and install required Bundler version
RUN gem update --system && \
    gem install bundler:$BUNDLER_VERSION 
```

`LANG=C.UTF-8`将默认区域设置为 UTF-8。否则 Ruby 将使用 US-ASCII 作为字符串，并与那些甜蜜的表情符号说拜拜👋

我们通过`GEM_HOME=/bundle`设置 gem 安装的路径。什么是`/bundle`？这是我们稍后将挂载为*卷*的路径，以保持对主机系统的依赖，即您的开发机器(参见下面的`docker-compose.yml`)。

`BUNDLE_PATH`和`BUNDLE_BIN`变量告诉 Bundler 在哪里寻找 gems 和 Ruby 可执行文件。

最后，我们在全球范围内公开 Ruby 和应用程序的二进制代码:

```
ENV PATH /app/bin:$BUNDLE_BIN:$PATH 
```

这允许我们运行`rails`、`rake`、`rspec`和其他*绑定的*命令，而不用在它们前面加上`bundle exec`。

## [`docker-compose.yml`T4】](https://github.com/evilmartians/terraforming-rails/blob/master/examples/dockerdev/docker-compose.yml)

Docker Compose 是一个编排我们容器化环境的工具。它允许我们将容器相互链接，定义持久卷和服务。

下面是一个典型的 Rails 应用程序开发的合成文件，使用 PostgreSQL 作为数据库，使用 Sidekiq 后台作业处理器:

```
version: '3.4'

services:
  app: &app
    build:
      context: .
      dockerfile: ./.dockerdev/Dockerfile
      args:
        RUBY_VERSION: '2.6.3'
        PG_MAJOR: '11'
        NODE_MAJOR: '11'
        YARN_VERSION: '1.13.0'
        BUNDLER_VERSION: '2.0.2'
    image: example-dev:1.0.0
    tmpfs:
      - /tmp

  backend: &backend
    <<: *app
    stdin_open: true
    tty: true
    volumes:
      - .:/app:cached
      - rails_cache:/app/tmp/cache
      - bundle:/bundle
      - node_modules:/app/node_modules
      - packs:/app/public/packs
      - .dockerdev/.psqlrc:/root/.psqlrc:ro
    environment:
      - NODE_ENV=development
      - RAILS_ENV=${RAILS_ENV:-development}
      - REDIS_URL=redis://redis:6379/
      - DATABASE_URL=postgres://postgres:postgres@postgres:5432
      - BOOTSNAP_CACHE_DIR=/bundle/bootsnap
      - WEBPACKER_DEV_SERVER_HOST=webpacker
      - WEB_CONCURRENCY=1
      - HISTFILE=/app/log/.bash_history
      - PSQL_HISTFILE=/app/log/.psql_history
      - EDITOR=vi
    depends_on:
      - postgres
      - redis

  runner:
    <<: *backend
    command: /bin/bash
    ports:
      - '3000:3000'
      - '3002:3002'

  rails:
    <<: *backend
    command: bundle exec rails server -b 0.0.0.0
    ports:
      - '3000:3000'

  sidekiq:
    <<: *backend
    command: bundle exec sidekiq -C config/sidekiq.yml

  postgres:
    image: postgres:11.1
    volumes:
      - .psqlrc:/root/.psqlrc:ro
      - postgres:/var/lib/postgresql/data
      - ./log:/root/log:cached
    environment:
      - PSQL_HISTFILE=/root/log/.psql_history
    ports:
      - 5432

  redis:
    image: redis:3.2-alpine
    volumes:
      - redis:/data
    ports:
      - 6379

  webpacker:
    <<: *app
    command: ./bin/webpack-dev-server
    ports:
      - '3035:3035'
    volumes:
      - .:/app:cached
      - bundle:/bundle
      - node_modules:/app/node_modules
      - packs:/app/public/packs
    environment:
      - NODE_ENV=${NODE_ENV:-development}
      - RAILS_ENV=${RAILS_ENV:-development}
      - WEBPACKER_DEV_SERVER_HOST=0.0.0.0

volumes:
  postgres:
  redis:
  bundle:
  node_modules:
  rails_cache:
  packs: 
```

我们定义**八个**服务。为什么这么多？其中一些只为其他人定义共享配置(*抽象*服务，如`app`和`backend`)，另一些则习惯于使用应用容器的特定命令(如`runner`)。

使用这种方法，我们不使用`docker-compose up`命令来运行我们的应用程序，而是总是指定我们想要运行的确切服务(例如`docker-compose up rails`)。这是有道理的:在开发中，你很少需要所有的服务都启动并运行(Webpacker、Sidekiq 等。).

让我们仔细看看每个服务。

### `app`

该服务的主要目的是提供构建我们的应用程序容器(在上面的`Dockerfile`中定义的容器)所需的所有信息:

```
build:
  context: .
  dockerfile: ./.dockerdev/Dockerfile
  args:
    RUBY_VERSION: '2.6.3'
    PG_MAJOR: '11'
    NODE_MAJOR: '11'
    YARN_VERSION: '1.13.0'
    BUNDLER_VERSION: '2.0.2' 
```

`context`目录为 Docker 定义了[构建上下文](https://docs.docker.com/compose/compose-file/#context):这有点像构建过程的工作目录，例如由`COPY`命令使用。

我们显式地指定了 Dockerfile 的路径，因为我们没有将它保存在项目根目录中，而是将所有与 Docker 相关的文件打包在一个隐藏的`.dockerdev`目录中。

而且，正如我们前面提到的，我们使用 Dockerfile 文件中声明的`args`来指定依赖项的确切版本。

我们应该注意的一件事是我们标记图像的方式:

```
image: example-dev:1.0.0 
```

使用 Docker 进行开发的一个好处是能够在团队中自动同步配置变更。您只需要在每次对本地映像版本进行更改时升级它(或者对它所依赖的参数或文件进行更改)。你能做的最糟糕的事情就是使用`example-dev:latest`作为你的构建标签。

保留一个镜像版本也有助于在两个不同的环境中工作，而不会有任何额外的麻烦。例如，当你在一个长期运行的“杂务/升级到 ruby-3”分支上工作时，你可以很容易地切换到`master`并使用旧 ruby 的旧映像，不需要重新构建任何东西。

> 你能做的最糟糕的事情就是在你的`docker-compose.yml`中为图像使用`latest`标签。

我们还*告诉* Docker 对[使用 tmpfs](https://docs.docker.com/v17.09/engine/admin/volumes/tmpfs/#choosing-the-tmpfs-or-mount-flag) 对容器内的`/tmp`文件夹进行加速:

```
tmpfs:
  - /tmp 
```

### `backend`

我们到达了这篇文章最有趣的部分。

这个服务定义了所有 Ruby 服务的共享行为。

先说卷数:

```
volumes:
  - .:/app:cached
  - bundle:/bundle
  - rails_cache:/app/tmp/cache
  - node_modules:/app/node_modules
  - packs:/app/public/packs
  - .dockerdev/.psqlrc:/root/.psqlrc:ro 
```

volumes 列表中的第一项使用`cached`策略将当前工作目录(项目的根目录)挂载到容器中的`/app`文件夹。这个`cached`修改器是 MacOS 上高效 Docker 开发的关键。我们不打算在这篇文章中深挖(我们正在做一个关于这个主题的独立文章😉)，但是你可以看看[的文档](https://docs.docker.com/docker-for-mac/osxfs-caching/)。

下一行*告诉*我们的容器使用一个名为`bundle`的卷来存储`/bundle`内容。通过这种方式，我们可以跨运行持久化我们的 gems 数据:在运行`docker-compose down --volumes`之前，`docker-compose.yml`中定义的所有卷都保持不变。

下面三行也是为了摆脱“Docker 在 Mac 上速度慢”的魔咒。我们将所有生成的文件放入 Docker 卷中，以避免主机上繁重的磁盘操作:

```
- rails_cache:/app/tmp/cache
- node_modules:/app/node_modules
- packs:/app/public/packs 
```

> 要让 Docker 在 MacOS 上足够快，请遵循以下两条规则:使用`:cached`挂载源文件，并使用卷存储生成的内容(资产、捆绑包等)。).

最后一行将特定的`psql`配置添加到容器中。我们主要需要它将命令历史保存在应用程序的`log/.psql_history`文件中。为什么`psql`在红宝石容器里？运行`rails dbconsole`时内部使用。

我们的 [`.psqlrc`](https://github.com/evilmartians/terraforming-rails/blob/master/examples/dockerdev/.dockerdev/.psqlrc) 文件包含以下技巧，使得通过 env 变量指定历史文件的路径成为可能(允许通过`PSQL_HISTFILE` env 变量指定历史文件的路径，否则回退到默认的`$HOME/.psql_history`):

```
\set HISTFILE `[[ -z $PSQL_HISTFILE ]] && echo $HOME/.psql_history || echo $PSQL_HISTFILE` 
```

我们来谈谈环境变量:

```
environment:
  - NODE_ENV=${NODE_ENV:-development}
  - RAILS_ENV=${RAILS_ENV:-development}
  - REDIS_URL=redis://redis:6379/
  - DATABASE_URL=postgres://postgres:postgres@postgres:5432
  - WEBPACKER_DEV_SERVER_HOST=webpacker
  - BOOTSNAP_CACHE_DIR=/bundle/bootsnap
  - HISTFILE=/app/log/.bash_history
  - PSQL_HISTFILE=/app/log/.psql_history
  - EDITOR=vi
  - MALLOC_ARENA_MAX=2
  - WEB_CONCURRENCY=${WEB_CONCURRENCY:-1} 
```

这里有几件事，我想重点说一件。

首先，`X=${X:-smth}`语法。它可以被翻译为“对于容器内的 X 变量，如果存在，则使用主机 X 环境变量值，否则使用另一个值”。*因此，我们可以在随命令一起提供的不同环境中运行服务，例如`RAILS_ENV=test docker-compose up rails`* 。

`DATABASE_URL`、`REDIS_URL`和`WEBPACKER_DEV_SERVER_HOST`变量*将我们的 Ruby 应用程序*连接到其他服务。Rails(分别是 ActiveRecord 和 Webpacker)开箱即用地支持`DATABASE_URL`和`WEBPACKER_DEV_SERVER_HOST`变量。有些库也支持`REDIS_URL`(Sidekiq)，但不是所有的库都支持(例如，Action Cable 必须明确配置)。

我们使用 [bootsnap](https://www.github.com/Shopify/bootsnap) 来加快应用程序的加载时间。我们将其缓存存储在与 Bundler 数据相同的卷中，因为该缓存主要包含 gems 数据；因此，举例来说，如果我们要进行另一次 Ruby 版本升级，我们应该放弃一切。

从开发人员的 UX 角度来看,`HISTFILE=/app/log/.bash_history`是一个重要的设置:它告诉 Bash 将其历史存储在指定的位置，从而使其持久化。

例如，`rails credentials:edit`命令使用`EDITOR=vi`来管理凭证文件。

最后，最后两个设置，`MALLOC_ARENA_MAX`和`WEB_CONCURRENCY`，有助于控制 Rails 内存处理。

这项服务中唯一尚未覆盖的线路是:

```
stdin_open: true
tty: true 
```

他们让这个服务*交互*，也就是提供一个 TTY。例如，我们需要它在容器中运行 Rails 控制台或 Bash。

这与使用`-it`选项运行 Docker 容器是一样的。

### `webpacker`

这里我唯一想提到的是`WEBPACKER_DEV_SERVER_HOST=0.0.0.0`设置:它使 Webpack dev 服务器可以从外部的*访问(默认情况下，它运行在`localhost`)。*

### `runner`

为了解释这个服务的用途，让我分享一下我使用 Docker 进行开发的方式:

*   我启动了一个 Docker 守护进程，运行一个定制的`docker-start`脚本:

```
#!/bin/sh

if ! $(docker info > /dev/null 2>&1); then echo "Opening Docker for Mac..."
  open -a /Applications/Docker.app
  while ! docker system info > /dev/null 2>&1; do sleep 1; done echo "Docker is ready to rock!"
else echo "Docker is up and running."
fi 
```

*   然后我在项目目录中运行`dcr runner` ( `dcr`是`docker-compose run`的别名)登录到容器的 shell 中；这是以下各项的别名:

```
$ docker-compose run --rm runner 
```

*   我在这个容器中运行(几乎)一切:测试、迁移、Rake 任务等等。

如您所见，我不会在每次需要运行任务时都使用新的容器，我总是使用同一个容器。

因此，我使用`dcr runner`的方式和几年前使用`vagrant ssh`的方式一样。

例如，它被称为`runner`而不是`shell`的唯一原因是，它也可以被用于*在一个容器内运行任意命令。*

**注意**:这个`runner`服务是一个品味问题，除了默认的`command` ( `/bin/bash`)之外，它并没有带来任何新的东西。因此，`docker-compose run runner`与`docker-compose run web /bin/bash`完全相同(但更短😉).

## 奖金: [dip.yml](https://github.com/evilmartians/terraforming-rails/blob/master/examples/dockerdev/dip.yml)

如果你仍然认为 *Docker Compose* 方式太复杂，有一个叫做 [Dip](https://github.com/bibendi/dip) 的工具，是我在邪恶火星人的一个同事开发的，旨在使开发者体验更流畅。

如果您有多个合成文件或依赖于平台的配置，它尤其有用，因为它可以将它们粘合在一起，并提供一个通用接口来管理 Docker 开发环境。

我们将在未来告诉你更多关于它的事情。敬请期待！

* * *

特别感谢 [Sergey Ponomarev](https://github.com/sponomarev) 和 [Mikhail Merkushin](https://github.com/bibendi) 分享他们在这个问题上的建议。🤘

* * *

阅读更多关于 https://evilmartians.com/chronicles 的文章！