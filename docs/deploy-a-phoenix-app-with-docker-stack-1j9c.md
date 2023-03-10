# 使用 Docker 堆栈部署 Phoenix 应用程序

> 原文：<https://dev.to/ilsanto/deploy-a-phoenix-app-with-docker-stack-1j9c>

# 强制引见

大家好，这是我在 DEV 上的第一篇帖子，我希望它尽可能简短，以便任何人都可以直奔主题，如果在 docker 上部署 Phoenix 应用程序是手头的问题。

我们将利用与 Elixir 1.9 一起发布的新“混合发布”特性。

我会假设你的应用程序需要一个 Postgres 数据库。如果您的架构比这更复杂(Redis、Mongo 等)，那么您的架构中包含的任何其他软件的部署策略都超出了本文的范围。

好了，我们走吧！

# 本地释放...

## ...第一次没有码头工人

在下面的例子中，我们的应用程序的名称是 demo(所以用您的应用程序的真实名称替换任何出现的“Demo”)。

首先，我们必须确保我们的应用程序将在本地与生产环境设置“混合发布”。

在您的控制台中，在项目的根目录下运行以下命令:

```
mix release.init 
```

然后在项目的 **/config** 目录:
中创建一个 **releases.exs** 文件

```
import Config

secret_key_base = System.fetch_env!("SECRET_KEY_BASE")
app_port = System.fetch_env!("APP_PORT")
app_hostname = System.fetch_env!("APP_HOSTNAME")
db_user = System.fetch_env!("DB_USER")
db_password = System.fetch_env!("DB_PASSWORD")
db_host = System.fetch_env!("DB_HOST")

config :demo, DemoWeb.Endpoint,
  http: [:inet6, port: String.to_integer(app_port)],
  secret_key_base: secret_key_base

config :demo,
  app_port: app_port

config :demo,
  app_hostname: app_hostname

# Configure your database
config :demo, Demo.Repo,
  username: db_user,
  password: db_password,
  database: "demo_prod",
  hostname: db_host,
  pool_size: 10 
```

我们将把我们所有的生产“秘密”保存在**中。env** 文件在我们项目的根目录:

```
APP_PORT=4000
APP_HOSTNAME=localhost
DB_USER=postgres
DB_PASSWORD=pass
DB_HOST=localhost
SECRET_KEY_BASE=Y0uRvErYsecr3TANDL0ngStr1ng 
```

**APP_HOSTNAME** 将会是 *localhost* ，用于在本地测试你的应用，但是稍后它将需要被设置为你的真实域名(例如:【myverycoolapp.com】)，正如你在 **/config/prod.exs** 的注释中看到的，它需要被编辑如下，以便从我们的**获取*主机*和*端口*。env** 文件。确保取消最后一行的注释，并从文件中删除***" import _ config " prod . secret . exs "***(因为我们的“秘密”在**中)。env** ):

```
use Mix.Config

# Don't forget to configure the url host to something meaningful,
# Phoenix uses this information when generating URLs.
config :demo, DemoWeb.Endpoint,
  load_from_system_env: true,
  url: [host: Application.get_env(:demo, :app_hostname), port: Application.get_env(:demo, :app_port)],
  cache_static_manifest: "priv/static/cache_manifest.json"

# Do not print debug messages in production
config :logger, level: :info

# Which server to start per endpoint:
#
config :demo, DemoWeb.Endpoint, server: true 
```

记得编辑**/lib/demo _ web/endpoint . ex**:
中的 **init/2**

```
 @doc """
  Callback invoked for dynamically configuring the endpoint.

  It receives the endpoint configuration and checks if
  configuration should be loaded from the system environment.
  """
  def init(_key, config) do
    if config[:load_from_system_env] do
      port = Application.get_env(:demo, :app_port) || raise "expected the PORT environment variable to be set"
      {:ok, Keyword.put(config, :http, [:inet6, port: port])}
    else
      {:ok, config}
    end
  end 
```

为了能够在发布的应用中管理我们的迁移，我们需要创建一个**/lib/demo/release . ex**:

```
defmodule Demo.Release do
  @app :demo

  def migrate do
    for repo <- repos() do
      {:ok, _, _} = Ecto.Migrator.with_repo(repo, &Ecto.Migrator.run(&1, :up, all: true))
    end
  end

  def rollback(repo, version) do
    {:ok, _, _} = Ecto.Migrator.with_repo(repo, &Ecto.Migrator.run(&1, :down, to: version))
  end

  defp repos do
    Application.load(@app)
    Application.fetch_env!(@app, :ecto_repos)
  end
end 
```

好了，我们准备尝试在本地发布我们的应用程序。

你所要做的就是在你的控制台中执行以下命令:

```
mix phx.digest
MIX_ENV=prod mix release 
```

如果没有出错，您将会收到以下说明:

```
* assembling demo-0.1.0 on MIX_ENV=prod                                                                                                               
* using config/releases.exs to configure the release at runtime                                                                                       
* skipping elixir.bat for windows (bin/elixir.bat not found in the Elixir installation)                                                               
* skipping iex.bat for windows (bin/iex.bat not found in the Elixir installation)                                                                     

Release created at _build/prod/rel/demo!

    # To start your system
    _build/prod/rel/demo/bin/demo start

Once the release is running:

    # To connect to it remotely
    _build/prod/rel/demo/bin/demo remote

    # To stop it gracefully (you may also send SIGINT/SIGTERM)
    _build/prod/rel/demo/bin/demo stop

To list all commands:

    _build/prod/rel/demo/bin/demo 
```

但是在我们可以开始并尝试我们发布的应用程序之前，我们需要迁移我们的数据库，在我们的控制台中键入以下命令:

```
source .env
_build/prod/rel/demo/bin/demo eval Demo.Release.migrate 
```

然后，你可以按照上面的建议启动你的应用:

```
_build/prod/rel/demo/bin/demo start 
```

你的应用程序工作正常吗？我希望如此。如果是的话，我们可以继续。

## ...然后用 docker

我们希望我们的应用程序尽可能轻，所以我们将使用两张基于*灵药:阿尔卑斯山*和*阿尔卑斯山*的 docker 图像。

我们将有一个多级 Dockerfile 文件。在第一阶段，我们将构建我们的版本，在第二阶段，我们将部署我们发布的应用程序和 postgres 客户端(我们将使用它来了解数据库是否准备好接受连接和运行我们的迁移)。

这是我们的**文档**，我建议你仔细阅读:

```
# ---- Build Stage ----
FROM elixir:alpine AS app_builder

# Set environment variables for building the application
ENV MIX_ENV=prod \
    TEST=1 \
    LANG=C.UTF-8

RUN apk add --update git && \
    rm -rf /var/cache/apk/*

# Install hex and rebar
RUN mix local.hex --force && \
    mix local.rebar --force

# Create the application build directory
RUN mkdir /app
WORKDIR /app

# Copy over all the necessary application files and directories
COPY config ./config
COPY lib ./lib
COPY priv ./priv
COPY mix.exs .
COPY mix.lock .

# Fetch the application dependencies and build the application
RUN mix deps.get
RUN mix deps.compile
RUN mix phx.digest
RUN mix release

# ---- Application Stage ----
FROM alpine AS app

ENV LANG=C.UTF-8

# Install openssl
RUN apk add --update openssl ncurses-libs postgresql-client && \
    rm -rf /var/cache/apk/*

# Copy over the build artifact from the previous step and create a non root user
RUN adduser -D -h /home/app app
WORKDIR /home/app
COPY --from=app_builder /app/_build .
RUN chown -R app: ./prod
USER app

COPY entrypoint.sh .

# Run the Phoenix app
CMD ["./entrypoint.sh"] 
```

在项目的根目录
下创建一个 **entrypoint.sh** (并使其可执行)

```
#!/bin/sh
# Docker entrypoint script.

# Wait until Postgres is ready
while ! pg_isready -q -h $DB_HOST -p 5432 -U $DB_USER
do echo "$(date) - waiting for database to start"
  sleep 2
done

./prod/rel/demo/bin/demo eval Demo.Release.migrate

./prod/rel/demo/bin/demo start 
```

现在我们可以建立我们的形象:

```
docker build -t demo-app . 
```

由于我们需要运行 postgres 实例，这里有一个 **docker-compose.yml** 将负责我们的应用程序和数据库:

```
version: '3.1'

services:

  database:
    image: postgres
    restart: always
    environment:
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: demo_prod

  web:
    image: demo-app
    restart: always
    ports:
      - ${APP_PORT}:${APP_PORT}
    environment:
      APP_PORT: ${APP_PORT}
      DB_USER: ${DB_USER}
      DB_PASSWORD: ${DB_PASSWORD}
      DB_HOST: ${DB_HOST}
      SECRET_KEY_BASE: ${SECRET_KEY_BASE}
    depends_on:
      - database 
```

现在你需要编辑你的**。env** 并至少更改**DB _ HOST*(您可以保持 DB 凭证不变，数据库容器会为您创建用户和 DB):* 

```
APP_PORT=4000
APP_HOSTNAME=localhost
DB_USER=postgres
DB_PASSWORD=pass
DB_HOST=database
SECRET_KEY_BASE=Y0uRvErYsecr3TANDL0ngStr1ng 
```

现在你可以启动你的容器了:

```
docker-compose -f docker-compose.yml up 
```

如果一切正常，你可以将浏览器指向*[http://localhost:4000](http://localhost:4000)*，你的应用程序就会在那里等着你。

现在我们准备编写我们的 **docker-stack.yml** ，这样我们就可以在生产中部署我们的应用程序(在 DigitalOcean droplet 中，在 AWS 上，在您自己的服务器上，...):

```
version: '3.1'

services:

  database:
    image: postgres
    deploy:
      restart_policy:
        condition: on-failure
    environment:
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: demo_prod
    networks:
      - backend

  web:
    image: foobar/demo-app:latest
    deploy:
      restart_policy:
        condition: on-failure
    ports:
      - ${APP_PORT}:${APP_PORT}
    environment:
      APP_PORT: ${APP_PORT}
      APP_HOSTNAME: ${APP_HOSTNAME}
      DB_USER: ${DB_USER}
      DB_PASSWORD: ${DB_PASSWORD}
      DB_HOST: ${DB_HOST}
      SECRET_KEY_BASE: ${SECRET_KEY_BASE}
    depends_on:
      - database_migrator
    networks:
      - backend

networks:
  backend: 
```

在部署之前，我们需要将我们的应用程序发布到我们选择的 docker-hub(在上面的例子中，它被发布为虚构的 *foobar/demo-app:latest* )。将图像发布到 docker-hub 超出了本文的范围，但是如果您在这里，我确信您已经知道如何做了...

现在我们必须创建一个**。我们部署的环境堆栈**:

```
APP_PORT=4000
APP_HOSTNAME=mycoolapp.com
DB_USER=postgres
DB_PASSWORD=pass
DB_HOST=database
SECRET_KEY_BASE=Y0uRvErYsecr3TANDL0ngStr1ng 
```

最后，在你设置好你的 swarm 并连接到它之后(不在此范围内，[参见 docs.docker.com](https://docs.docker.com/machine/drivers/)上的文档)，你可以如下部署你的应用:

```
source .env-stack
docker stack deploy -c docker-stack.yml demo-app 
```

假设你的 swarm 配置为响应主机名 mycoolapp.com，将你的浏览器指向就可以了！

简单，嗯？:-)

我期待着你所有建设性的(但不仅仅是)批评和建议。

感谢和下一个。*