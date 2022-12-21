# 真实世界凤凰| >让我们授权一些用户|> Pow！

> 原文：<https://dev.to/kabisasoftware/real-world-phoenix-let-s-auth-some-users-pow-ho7>

如果你已经在 Rails 世界生活了一段时间，你已经被生态系统中的用户认证库宠坏了。一个流行的选择是经常设计的
,这使得用户认证变得轻而易举。在 Elixir / Phoenix land
中，已经有一些可用的选项，但我最近遇到了一个新的“kid
on the block”，旨在使开发者的用户认证变得轻而易举，
同时仍然保持 Elixir 的明确和模块化的心态进入
帐户。

这个库叫做 [Pow](https://github.com/danschultzer/pow) 。正如 Dan Schultzer 在他关于 Elixir 论坛的介绍性文章中提到的，他基本上缺少这样一个库。我认为在长生不老药的世界里我们需要更多的丹。我也注意到
对 bug 和特性请求的反应是多么的迅速和周到，所以我希望我们都能从他的热情中学习。

## 异能

以下是他第一次介绍自己图书馆的片段:

当前的解决方案都不太适合我，所以我从头开始构建了一个用户管理系统。这个项目花费的时间比我最初想象的要长得多，我很想得到一些帮助来解决所有问题。
所以请尝试一下，并告诉我你的想法！

## 现实世界的凤凰

在这一系列真实世界的 Phoenix 帖子中，我想探索一些方法，我们可以使用 Phoenix 来构建一个更简单的应用程序，而不必显式地使用 OTP 框架(我们当然是隐式地使用 OTP 框架，因为 Phoenix 利用了这一点；) ).在我们的旅程中，我们将发现我们可以使用哪些库，以及我们可以不使用任何库的地方，这就是灵丹妙药！除了程序员的工作，我还教架子鼓，并需要管理学生信息、课堂笔记和许多其他与教学相关的东西。那么，为什么不开发一个管理学生信息的应用程序和一些工具，让教师的生活更容易管理呢？

## 用户管理/认证

在第一次探索中，我想通过设置用户验证来启动应用程序。几乎所有的应用程序最终都需要某种认证。当然，在某个时候它会成为定制的，但是在开始的时候，它对每个应用程序来说都是一样的。让我们看看电力如何让我们尽快启动并运行。

## 启动器模板

作为开始，我使用了我的实时视图 Dashboard starter 应用程序，只是因为它很方便，而且已经有了一些默认设置。我增加了埃克托和波斯格里斯。有关该模板的更多信息，请查看我的报告:[gen _ template _ phx _ live _ view](https://github.com/drumusician/gen_template_phx_live_view)

好了，一旦我们有了启动和运行，让我们打开 Pow readme，看看它能让我们走多远。

因此，我们需要做的第一件事是将 Pow 依赖项添加到我们的 mix.exs 文件:

```
def deps do
# ...
{:pow, "~> 1.0.7"}
  # ...
end 
```

然后运行`mix deps.get`来安装依赖项。

Dan 显然已经考虑到了他的最终用户，因为他也有一个关于你何时设置保护伞应用程序的指南。干得好，丹！他还加入了一个小的混合任务来让我们快速启动和运行。

```
mix pow.install 
```

运行上面的命令后，我们获得了大量关于下一步应该做什么的信息。Pow 为我们创建了两个文件。一个文件在`LIB_PATH/users/user.ex`中，一个迁移在`PRIV_PATH/repo/migrations/TIMESTAMP_create_user.ex`中。我已经
知道我将更喜欢有界的帐户上下文，所以我将把`users/user.ex`改为`accounts/user.ex`。这是可能，因为 pow 提供了一个配置选项，所以您可以实际更改将用于身份验证的用户。

您可以在`config/config.exs`中提供配置选项:

```
# config/config.exs
config :student_manager, :pow
  user: Studentmanager.Accounts.User,
  repo: StudentManager.Repo 
```

然后我们需要在已经存在的`plug Plug.Session...`之后设置一个会话插件:

```
# WEB_PATH/endpoint.exs
# ...
  plug Plug.Session,
    store: :cookie,
    key: "_my_app_key",
    signing_salt: "secret"

  plug Pow.Plug.Session, otp_app: :student_manager
# ... 
```

Pow 还提供了一个功能，可以扩展到一些基本的用户注册和验证路由，您可以将这些路由添加到您的 routes 文件中:

```
# ... pipelines

  pipeline :protected do
    plug Pow.Plug.RequireAuthenticated,
      error_handler: Pow.Phoenix.PlugErrorHandler
  end

  scope "/" do
    pipe_through :browser

    pow_routes() # <= magic pow routes!
  end

  scope "/", MyAppWeb do
    pipe_through [:browser, :protected]

    # Add your protected routes here
  end

  # ... routes 
```

现在，一旦您运行`mix ecto.setup`让您的数据库启动并运行，您就可以启动 Phoenix 并通过访问用户注册路径:`http://localhost:4000/registration/new`创建一个新用户

## 结论

现在，让用户注册和基于会话的身份验证运行起来非常简单。当然，Pow 提供了各种定制选项，你也可以生成用于你自己项目的模板，这样你就可以完全定制了。我们将暂时保留这一点，在下一篇文章中，我们将扩展我们的用户模型并实现用户角色，以便教师可以管理他/她的学生。

希望你喜欢这篇文章，下次再见！