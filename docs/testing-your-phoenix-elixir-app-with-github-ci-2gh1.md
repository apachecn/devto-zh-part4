# 使用 Github CI 测试您的 Elixir + Phoenix + Postgres 应用程序

> 原文：<https://dev.to/nickforall/testing-your-phoenix-elixir-app-with-github-ci-2gh1>

最初发布于 [nickvernij.nl](https://nickvernij.nl/blog/github-ci-elixir.html?ref=dev.to)

GitHub 最近发布了他们自己的 CI，它的 UI 和营销看起来非常花哨。我不得不建立一个新的长生不老药+凤凰项目，并决定尝试一下。

我花了整整一个上午才拿到绿色支票，因为默认的 elixir 模板非常小，而且创建工作流的文档需要大量阅读才能完成。

有一些角落和缝隙需要通过，但当你让它工作时，它是非常强大的。从本质上讲，默认的 Elixir 工作流对于测试您的普通 Elixir 库是很好的，但是 Phoenix 应用程序将需要一些额外的设置和服务。在我的例子中，我需要建立一个 PostgreSQL 服务。

首先我们要设置`MIX_ENV`环境变量，所以当我们使用 ecto 时，它知道应该设置测试数据库。我们可以在定义运行测试的容器时定义全局环境变量。

```
 container:
      image: elixir:1.9.1-slim
      env:
        MIX_ENV: test 
```

Enter fullscreen mode Exit fullscreen mode

## 设置数据库

但是要使用 ecto，我们实际上需要一个数据库。在工作流定义中，我们可以定义一个服务列表。这些是 docker 映像，是运行我们的测试步骤的先决条件。

这是我的服务配置的样子:

```
 services:
      postgres:
        image: postgres
        ports:
          - 5432:5432
        env:
          POSTGRES_PASSWORD: postgres
          POSTGRES_USER: postgres
        options: --health-cmd pg_isready --health-interval 10s --health-timeout 5s --health-retries 3 
```

Enter fullscreen mode Exit fullscreen mode

**让我们逐条浏览一下服务定义:**

`image: postgres`定义引入哪个 docker 图像，在我的例子中，我使用 Postgres 的官方 docker 图像:[https://hub.docker.com/_/postgres](https://hub.docker.com/_/postgres)T3】

```
ports:
    - 5432:5432 
```

Enter fullscreen mode Exit fullscreen mode

我们总是必须定义我们想要为 docker 容器公开哪些端口。在这种情况下，我只是公开默认的 Postgres 端口

```
env:
  POSTGRES_PASSWORD: postgres
  POSTGRES_USER: postgres 
```

Enter fullscreen mode Exit fullscreen mode

我们使用的 docker 容器定义了一些环境变量，它将使用这些变量来配置 Postgres 服务器。这些变量的完整列表可以在 [Postgres Docker Hub 描述](https://hub.docker.com/_/postgres)中找到。

```
options: --health-cmd pg_isready --health-interval 10s --health-timeout 5s --health-retries 5 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们必须传递一些额外的选项。这些选项将直接映射到`docker create`命令。我们需要添加这个健康检查的原因是因为`docker create`在容器实际准备好被使用之前就已经存在了。

幸运的是，有一个简单的命令`pg_isready`可用，我们可以检查它。一旦 Postgres 容器准备就绪，您的测试运行将继续。

## 与数据库连接

默认的 Elixir 工作流应该已经定义了一些`steps`。我们稍微修改了一下`Run Tests`步骤，这样它将创建一个数据库并传递一些额外的环境变量。

所有定义的服务将在网络上创建一个虚拟主机，以服务命名。这意味着您不能简单地访问本地主机上的 Postgres。

我已经在我的运行步骤中添加了一个环境变量，它公开了服务的名称。

```
 - name: Run Tests
      run: mix test
      env:
        DB_HOST: postgres 
```

Enter fullscreen mode Exit fullscreen mode

此外，我修改了我的混合测试配置来读取这个变量，并在默认情况下返回到 localhost 上

```
# Configure your database
config :myapp, MyApp.Repo,
  username: "postgres",
  password: "postgres",
  database: "myapp_test",
  hostname: System.get_env("DB_HOST", "localhost"),
  pool: Ecto.Adapters.SQL.Sandbox 
```

Enter fullscreen mode Exit fullscreen mode

## 运行测试

默认情况下，phoenix 应用程序应该为运行`ecto.setup`、`ecto.migrate`和`test`的`mix run test`起一个别名。

当向您的存储库推送新的提交时，工作流应该会触发，执行各种魔法并为您运行该命令。

你可以在这个 gist 中找到完整的文件:[https://gist . github . com/Nickforall/e 49 F5 F3 c 37414 e 05 f 9 a6 c 604 accf 2c 3 e](https://gist.github.com/Nickforall/e49f5f3c37414e05f9a6c604accf2c3e)

如果您有任何问题，请随时在 [twitter](https://twitter.com/nickforallnl) 上联系我

### 参考文献

*   [将 Github CI 用于 Elixir 项目](http://rocket-science.ru/hacking/2019/08/19/use-github-ci-for-elixir-projects?utm_campaign=elixir_radar_204&utm_medium=email&utm_source=RD+Station)
*   [https://help . github . com/en/articles/workflow-syntax-for-github-actions # jobs job _ id services](https://help.github.com/en/articles/workflow-syntax-for-github-actions#jobsjob_idservices)引用于 2019 年 8 月 29 日