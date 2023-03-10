# 用 Ueberauth 在 Phoenix 上制作简单的社会登录

> 原文：<https://dev.to/miraicode/social-login-phoenix-ueberauth-3am8>

# 简介

很多人都想开始使用 Phoenix 框架来制作一个 Web App，但却陷入了如何启动 Login 系统的问题。～

所以我建议为我们的 Web App 制作一个简单的社交登录(一个接一个)，使用`ueberauth``ueberauth`。

首先，让我们先介绍一下，`ueberauth`，这是一个帮助我们实现基于插件的 Web 应用程序授权的库，也就是说。

*   脸书，
*   推特
*   谷歌
*   开源代码库
*   [等、](https://github.com/ueberauth/ueberauth/wiki/List-of-Strategies)等

t0**[注 t1]:在这个案子里，每个人都已经足够熟悉 Phoenix 框架了，因为它只会在**

* * *

# 添加 deps！

呃，从在子项目～
中加入`ueberauth`到“dependencies”。

```
# mix.exs
defp deps do
  [{:ueberauth, "~> 0.6.1"}]
end 
```

Enter fullscreen mode Exit fullscreen mode

但等一下！我们要去的普罗维登斯都可以，我要去的地方是 T0 .. .

```
# mix.exs
defp deps do
  [
    {:ueberauth, "~> 0.6.1"},
    {:ueberauth_facebook, "~> 0.8.0"}
  ]
end 
```

Enter fullscreen mode Exit fullscreen mode

别忘了点 t0 .. . huff .. .

* * *

# 配置

现在让我们给 ueberauth 添加一个 config，看看他们会使用什么样的 provider 和 client secret 这样的创意。

```
# config/config.exs
config :ueberauth, Ueberauth,
  providers: [
    facebook: {Ueberauth.Strategy.Facebook, []}
]

# config/dev.exs
config :ueberauth, Ueberauth.Strategy.Facebook.OAuth,
  client_id: System.get_env("FACEBOOK_CLIENT_ID"),
  client_secret: System.get_env("FACEBOOK_CLIENT_SECRET") 
```

Enter fullscreen mode Exit fullscreen mode

**【备注:**可以从 Facebook 的“开发人员控制台”中找到客户端 id 和客户端 secret。

* * *

# 路由器&控制器

在 config 完成之后，我们来创建控制器，并为 login[t0 }管理路由。

```
# controllers/auth_controller.ex
defmodule NorzeWeb.AuthController do
  @moduledoc false

  use NorzeWeb, :controller
  plug Ueberauth

  alias Norze.Accounts

  def delete(conn, _params) do
    conn
    |> configure_session(drop: true)
    |> redirect(to: "/")
  end

  def callback(%{assigns: %{ueberauth_failure: _fails}} = conn, _params) do
    conn
    |> put_flash(:error, "Failed to authenticate.")
    |> redirect(to: "/")
  end

  def callback(%{assigns: %{ueberauth_auth: auth}} = conn, _params) do
    case Accounts.from_auth(auth) do
      {:ok, user} ->
        conn
        |> put_session(:current_user_id, user.id)
        |> configure_session(renew: true)
        |> redirect(to: "/")

      {:error, _} ->
        conn
        |> put_flash(:error, "Failed authenticated.")
        |> redirect(to: "/")
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

回调函数负责在 ueberauth 主体完成 authen user 操作后再接收请求，它将像 uid、proprouth 一样返回给我们。

可以看出，对于 success 和 fail
函数，我们将有两个回调函数。对于 delete 函数，它只需删除 logout 时间序列。

接下来我们把控制器放在 route
上

```
# router.ex
scope "/auth", NorzeWeb do
    pipe_through :browser

    get "/logout", AuthController, :delete
    get "/:provider", AuthController, :request
    get "/:provider/callback", AuthController, :callback
end 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们准备好开始社交登录了！
鱼。我们可以看到，`/:provider``:delete`运行到我们没有编写的`:delete`函数中，不要惊慌，先生，这是 ueberauth plug 本身的故障。

* * *

# 让我们试试

啊，让我们来试试吧，
->[【http://localhost:4000/auth/Facebook】](http://localhost:4000/auth/facebook)。

一旦进入这个链接，我们将被 ueberauth 带到每个 provider 的 authen 页面，一旦完成，我们将被带回`callback_url`。

完成仪式！

* * *

# 奖金追踪

从上面的 callback 函数编写中，我们将在成功登录时保留 user_id，但问题是我们是否继续使用该值。

一点也不难，只要创建一个`plug`就可以了。给每个 web app 的主路由，我们通过
运行，由这个插件来执行一个值检查。

**如有** - >达到用户输入，则将`conn.assigns`连同`user_signed_in?``user_signed_in?`设置为>。

```
# plugs/authentication.ex
defmodule NorzeWeb.Plugs.SetCurrentUser do
  @moduledoc false

  import Plug.Conn

  alias Norze.Accounts

  def init(_params) do
  end

  def call(conn, _params) do
    user_id = Plug.Conn.get_session(conn, :current_user_id)

    if current_user = user_id && Accounts.get_user(user_id) do
      conn
      |> assign(:current_user, current_user)
      |> assign(:user_signed_in?, true)
    else
      conn
      |> assign(:current_user, nil)
      |> assign(:user_signed_in?, false)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如何在 route 中引入插件，它只需将我们的东西添加到 pipeline 中，即～
t0㎡就可以了。

```
# router.ex
  pipeline :browser do
    plug :accepts, ["html"]
    plug :fetch_session
    plug :fetch_flash
    plug :protect_from_forgery
    plug :put_secure_browser_headers
    plug Plugs.SetCurrentUser
  end 
```

Enter fullscreen mode Exit fullscreen mode

啊，这是一个完整的，我们现在可以在我们的模板内运行 user 的数据。～～t0㎡。

```
<div class="navbar-end">
    <div class="navbar-item">
        <div class="buttons">
            <%= if @conn.assigns.user_signed_in? do %>
                <a href="#" class="button is-info">
                    <strong><%= @conn.assigns.current_user.email %></strong>
                </a>
                <%= link gettext("logout"), to: Routes.auth_path(@conn, :delete), class: "button is-light" %>
            <% else %>
                <a href="<%= Routes.auth_path(@conn, :request, "facebook") %>" class="button is-link">
                    <strong><%= gettext("login with facebook") %></strong>
                </a>
            <% end %>
        </div>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

封面图片由来自 Pixabay 的 Gerd Altmann 拍摄