# 在 Windows10 上用 Docker 启动的 Phoenix、MySQL 和开发环境

> 原文：<https://dev.to/problemaresolt/elixir-api-elm-api-32hl>

# 小心！！！

这篇文章犯了严重的错误。
代码改写还没有完成，下次要抽时间重新制作。

# windows10でdockerとelmとphoenix

想把前端作为 elm 后端的 API，把 Elixir
数据库作为 Mysql 启动，感觉是自己试着做了。
我在试着错误地想，这东西满满的，能不能在环境中变得很拥挤。

作为课题

*   我想用 ShellScript 总结一下
*   Elm 没什么，集装箱很乱，这样可以吗？
*   因为很害怕安全，所以需要. env 这样的文件吗

如果有什么好办法请告诉我的话就太感谢了。

## 坞站复合

MySQL8.0 及更高版本尚未支持？ 因此定为 5.7。

mariaex 错误#222

[https://github . com/薛西斯/mariaex/issues/222](https://github.com/xerions/mariaex/issues/222)

```
version: '3'
services:
  db:
    image: mysql:5.7
    container_name: "dev_db"
    ports:
      - "3306:3306"
    volumes:
      - ./mysql/volumes:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: 'password'      
  api:
    build: ./
    container_name: "api_app"
    working_dir: /opt/app
    command: mix phx.server
    volumes:
      - .:/opt/app
    environment:
      MYSQL_ROOT_USERNAME: 'root'
      MYSQL_ROOT_PASSWORD: 'password'
      MYSQL_HOSTNAME: 'db'
      MYSQL_PORT: '3306'
    ports:
      - '4000:4000'
    depends_on:
      - db
  web:
    build: ./
    container_name: "web_app"
    ports:
      - "8000:8000"
    volumes:
      - ./web/volumes:/opt/app/web
    tty: true 
```

## 文件生成器的构建

为了制作背景的 API，启动 Phoenix。

```
docker-compose build api 
```

### MySQL 容器启动

启动 DB。输入
`-d`在后台移动。

```
docker-compose up -d db 
```

### 凤凰计划作成

由于缺省值为 postgreSQL，因此在此以 MySQL 启动。
(虽然没有什么特别的理由，但是如果是 postgrey 的话，请将 Docker-Compose.yml 修改为 postgrey )

```
docker-compose run --rm api mix phx.new . --database mysql --no-html 
```

#### 添加库

添加 dapter 的`{:plug_cowboy, "~> 1.0"},`

配置/混合. exs 内へ追加

```
[中略]
  defp deps do
    [
      {:phoenix, "~> 1.3.4"},
      {:phoenix_pubsub, "~> 1.0"},
      {:phoenix_ecto, "~> 3.2"},
      {:mariaex, ">= 0.0.0"},
      {:phoenix_html, "~> 2.10"},
      {:phoenix_live_reload, "~> 1.0", only: :dev},
      {:gettext, "~> 0.11"},
      {:plug_cowboy, "~> 1.0"},　#ココ
      {:cowboy, "~> 1.0"}
    ]
  end

[中略] 
```

#### 库安装

安装 Elixir 库。

```
docker-compose run --rm api mix deps.get 
```

#### 开发环境的数据库连接准备

\config\dev.exs 的改写

```
[中略]
# Configure your database
config :app, App.Repo,
  adapter: Ecto.Adapters.MySQL,
  username: "root",
  password: "password",
  database: "app_dev",
  hostname: "db",
  pool_size: 10
[中略] 
```

### 数据库迁移

更新数据库信息

```
docker-compose run --rm api mix ecto.create 
```

# 启动。

启动 APP。

```
docker-compose up -d 
```

`http://localhost:4000` でPhoenixの画面が
欢迎来到凤凰城！
一个高效的 web 框架，
不会影响速度和可维护性。

做得到就 OK 了。

### Elixir 版本确认

```
docker-compose run --rm app elixir -v 
```

### Phoenix 版本确认

```
docker-compose run --rm app mix phx.new --version 
```

## 进入文档容器内时

注意:请先用`docker ps`等确认
因为指定了在 Docker-compose 文件中设定的`container_name: "web_app"`，所以用 container name 进入 docker 内。

```
docker exec -it web_app //bin/sh 
```

这样就放入了 Docker 的集装箱内。

### elm 的设定

用 cd web/
转到 Elm 用的目录。

```
/opt/app/ #  cd web/ 
```

/opt/app/web # elm reactor
すると
前往 [http://localhost:8000](http://localhost:8000) 查看您的项目仪表盘。
ダッシュボードが[に表示されます。](http://localhost:8000)

## At the end of the operation

```
docker-compose down 
```

这样就有了 Elm 和 Elixir Phoenix 的环境。
在 Linux 等上编辑文件时的权限等还有很多课题
那时候大家是怎么做的呢？

# 参考页面

## 文档设置

*   Elixir/Phoenix+MySQL5.7 環境をDocker/Docker Composeで整え開発[https://qiita . com/studio 23c/items/510 a 12 Abd 53 f 3651 b5 B2](https://qiita.com/studio23c/items/510a12abd53f3651b5b2)
*   Elixir1.6 / Phoenix1.3の環境をDockerで整えてCRUDアプリを動 https://qiita.com/dd511805/items/13f3ea4255dc4d1a3fbb# #仙丹凤凰【かすまで】公式 https://hexdocs.pm/phoenix/overview.html

## 榆树

*   创建榆树应用程序[https://github.com/halfzebra/create-elm-app#getting-started](https://github.com/halfzebra/create-elm-app#getting-started)
*   在 Elm 上尝试包含异步 Http 通信的 spa[https://qi ita.com/sand/items/849 c 919 b0d 957 ee99 B9 d](https://qiita.com/sand/items/849c919b0d957ee99b9d)
*   榆树 0.19 で JSONを使ってjsと通信 https://qiita.com/hibohiboo/items/33a8dcf462bd8e4a55e0したメモ
*   榆树-朗的例子[https://elm-lang.org/examples](https://elm-lang.org/examples)