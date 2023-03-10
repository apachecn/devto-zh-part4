# 使用混合版本部署 Phoenix 应用程序

> 原文：<https://dev.to/render/deploy-a-phoenix-app-with-mix-releases-1e6d>

这是一个在[渲染](https://render.com)上部署带有 [Mix 版本](https://hexdocs.pm/mix/Mix.Tasks.Release.html)的[凤凰](https://phoenixframework.org)应用程序的指南，这是一个新的云平台，具有对 Elixir 的本地支持。

本例的完成代码是 GitHub 上的[。](https://github.com/render-examples/phoenix_hello)

## 入门

在终端中创建新的 Phoenix 应用程序。对于这个例子，我们不需要数据库，所以我们将把`--no-ecto`标志传递给`mix`。

```
 # install phx.new; feel free to change 1.4.9 to a different version
   $ mix archive.install hex phx_new 1.4.9
   $ mix phx.new phoenix_hello --no-ecto # also fetch and install dependencies
   $ cd phoenix_hello 
```

Enter fullscreen mode Exit fullscreen mode

## 配置混合发布

创建 Mix 发布所需的运行时配置。

1.  将`config/prod.secret.exs`重命名为`config/releases.exs`。
2.  将新的`config/releases.exs`文件中的`use Mix.Config`更改为`import Config`。
3.  取消`config/releases.exs`中以下行的注释:

```
 config :phoenix_hello, PhoenixHelloWeb.Endpoint, server: true # uncomment me! 
```

Enter fullscreen mode Exit fullscreen mode

1.  最后更新`config/prod.exs`删除底部的行`import_config "prod.secret.exs"`。

## 创建一个构建脚本

我们需要在每次推送 Git repo 时运行一系列命令来构建我们的应用程序，我们可以通过构建脚本来完成这一任务。在 repo 的根目录下创建一个名为`build.sh`的脚本:

```
 #!/usr/bin/env bash
   # Initial setup
   mix deps.get --only prod
   MIX_ENV=prod mix compile

   # Compile assets
   npm install --prefix ./assets
   npm run deploy --prefix ./assets
   mix phx.digest

   # Remove the existing release directory and build the release
   rm -rf "_build"
   MIX_ENV=prod mix release 
```

Enter fullscreen mode Exit fullscreen mode

在将脚本签入 g it 之前，确保脚本是可执行的:

```
 $ chmod a+x build.sh 
```

Enter fullscreen mode Exit fullscreen mode

## 更新你的应用程序进行渲染

更新`config/prod.exs`以更改下面的行。

```
 config :phoenix_hello, PhoenixHelloWeb.Endpoint,
     url: [host: "example.com", port: 80],
     cache_static_manifest: "priv/static/cache_manifest.json" 
```

Enter fullscreen mode Exit fullscreen mode

对此:

```
 config :phoenix_hello, PhoenixHelloWeb.Endpoint,
     url: [host: System.get_env("RENDER_EXTERNAL_HOSTNAME") || "localhost", port: 80],
     cache_static_manifest: "priv/static/cache_manifest.json", 
```

Enter fullscreen mode Exit fullscreen mode

Render 为`config/prod.exs`填充`RENDER_EXTERNAL_HOSTNAME`。

如果您将自定义域添加到渲染应用程序，请不要忘记将主机更改为您的新域。

## 在本地构建并测试您的版本

通过运行`./build.sh`在本地编译您的版本。输出应该是这样的:

```
 * assembling phoenix_hello-0.1.0 on MIX_ENV=prod
   * using config/releases.exs to configure the release at runtime
   * skipping elixir.bat for windows (bin/elixir.bat not found in the Elixir installation)
   * skipping iex.bat for windows (bin/iex.bat not found in the Elixir installation)

   Release created at _build/prod/rel/phoenix_hello!

       # To start your system
       _build/prod/rel/phoenix_hello/bin/phoenix_hello start

   Once the release is running:

       # To connect to it remotely
       _build/prod/rel/phoenix_hello/bin/phoenix_hello remote

       # To stop it gracefully (you may also send SIGINT/SIGTERM)
       _build/prod/rel/phoenix_hello/bin/phoenix_hello stop

   To list all commands:

       _build/prod/rel/phoenix_hello/bin/phoenix_hello 
```

Enter fullscreen mode Exit fullscreen mode

通过运行以下命令并导航到 [http://localhost:4000](http://localhost:4000) 来测试您的版本。

```
SECRET_KEY_BASE=`mix phx.gen.secret` _build/prod/rel/phoenix_hello/bin/phoenix_hello start 
```

Enter fullscreen mode Exit fullscreen mode

如果一切看起来都很好，把你的改变推进到你的回购中。您现在可以在生产中部署您的应用程序了！🎉

## 部署渲染

1.  在 Render 上创建一个新的 **Web 服务**，并授予 Render 访问 Phoenix repo 的权限。

2.  在创建过程中使用以下值:

    | 田 | 价值 |
    | --- | --- |
    | **环境** | `Elixir` |
    | **构建命令** | `./build.sh` |
    | **开始命令** | `_build/prod/rel/phoenix_hello/bin/phoenix_hello start` |

    在**高级**部分下，添加以下环境变量:

    | 钥匙 | 价值 |
    | --- | --- |
    | `SECRET_KEY_BASE` | 一个足够强大的秘密。通过运行`mix phx.gen.secret`在本地生成它 |

就是这样！构建完成后，用 Mix 版本构建的 Phoenix web 服务将立即出现在您的渲染 URL 上。今后，GitHub repo 的每一次推送都将自动更新和部署您的应用，而不会停机。💯