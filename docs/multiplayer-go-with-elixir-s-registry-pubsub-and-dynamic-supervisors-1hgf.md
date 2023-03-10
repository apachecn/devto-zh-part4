# 多人去与药剂的注册表，PubSub 和动态监督

> 原文：<https://dev.to/appsignal/multiplayer-go-with-elixir-s-registry-pubsub-and-dynamic-supervisors-1hgf>

欢迎回到 Elixir Alchemy，以及使用 Phoenix LiveView 实现围棋游戏系列的第三部分。在[第一部分](https://blog.appsignal.com/2019/06/18/elixir-alchemy-building-go-with-phoenix-live-view.html)中，我们使用 LiveView 设置了游戏，在[第二部分](https://blog.appsignal.com/2019/07/04/elixir-alchemy-building-go-in-elixir-time-travel-and-the-ko-rule.html)中，我们添加了游戏历史并实现了 ko 规则。

目前，我们的游戏是一个本地的“热座”多人游戏，玩家轮流在同一个浏览器窗口。我们的下一个冒险旨在通过允许玩家在线与其他人一起玩，将游戏变成真正的多人游戏体验。

在本文中，我们将朝着这个目标迈出第一步。我们将允许创建新游戏，并通过将游戏结构转变为动态监督的 GenServer 来邀请其他人加入，我们将使用 Elixir 的注册表来跟踪游戏，我们将允许玩家连接到已经开始的游戏，我们将使用`Phoenix.PubSub`向所有连接的玩家广播移动。

## 我们离开的地方

这一集的[启动应用](https://github.com/jeffkreeftmeijer/hayago/tree/starter-3)是我们上次停止的地方。我们有一个游戏的实现，用按钮来撤销和重做移动。

最终结果可以在线播放，完成的应用程序的代码可以在库的[主分支](https://github.com/jeffkreeftmeijer/hayago/tree/master)中找到(如果你喜欢直接进入代码)。

我们有很多事情要做，所以让我们开始吧！

## 动态监督发电服务器

目前，游戏的状态保存在玩家的套接字连接中。这适用于单人游戏，但是没有办法让另一个套接字连接访问现有的游戏。为了允许两个玩家通过两个套接字连接一起玩游戏，我们需要将每个启动的游戏存储在一个双方都可以访问的独立进程中。

我们将使用一个动态监督的 GenServer 来保持每个游戏的状态。动态监管器允许玩家开始游戏，当游戏进程崩溃时，它还会自动重启游戏。

首先，让我们将游戏模块转换成一个 GenServer，以允许多个进程访问它。

```
# lib/hayago/game.ex
defmodule Hayago.Game do
  # ...
  use GenServer

  def start_link(options) do
    GenServer.start_link(__MODULE__, %Game{}, options)
  end

  @impl true
  def init(game) do
    {:ok, game}
  end

  @impl true
  def handle_call(:game, _from, game) do
    {:reply, game, game}
  end

  @impl true
  def handle_cast({:place, position}, game) do
    {:noreply, Game.place(game, position)}
  end

  @impl true
  def handle_cast({:jump, destination}, game) do
    {:noreply, Game.jump(game, destination)}
  end

  # ...
end 
```

我们的 GenServer 处理`:game`调用，该调用返回当前游戏的`Game`结构。`{:place, position}`和`{:jump, destination}`演员回调分别通过调用`Game.place/2`和`Game.jump/2`来更新游戏。

有了我们的 GenServer 回调，我们可以生成一个保持游戏状态的进程。为了监督这些过程，我们将使用 [Elixir 的`DynamicSupervisor`](https://hexdocs.pm/elixir/master/DynamicSupervisor.html) ，它允许按需繁殖后代。当玩家打开页面时，我们将用它来开始一个新游戏。

```
# lib/hayago/application.ex
defmodule Hayago.Application do
  # ...

  def start(_type, _args) do
    # List all child processes to be supervised
    children = [
      HayagoWeb.Endpoint,
      {DynamicSupervisor, strategy: :one_for_one, name: Hayago.GameSupervisor}
    ]

    opts = [strategy: :one_for_one, name: Hayago.Supervisor]
    Supervisor.start_link(children, opts)
  end

  # ...
end 
```

我们将在应用程序的`Application`模块中设置我们的动态主管。`:one_for_one`策略(这是动态监管者唯一可用的策略)确保游戏在崩溃时重启。

## PID，原子和注册表

因为我们的游戏模块现在实现了 GenServer 回调，所以我们可以通过 GenServer 模块生成一个游戏。

```
% iex -S mix
iex(1)> {:ok, pid} = DynamicSupervisor.start_child(Hayago.GameSupervisor, Hayago.Game)
{:ok, #PID<0.286.0>}
iex(2)> GenServer.cast(pid, {:place, 0})
:ok 
```

返回的 pid 用于获取和更新游戏的状态。在本例中，我们用它在棋盘的左上角位置放置一块石头。

我们可以将 pid 添加到套接字中，然后通过它请求和更新它的状态，而不是为每个连接将游戏保存在套接字中。

然而，我们的主管负责重启任何崩溃的游戏进程。当这种情况发生时，游戏会在一个新的进程中使用新的 pid 重新启动。当这种情况发生时，玩家仍然会与游戏断开连接，因为他们对游戏的唯一引用将不再有效，他们将无法获得新的 pid。

```
iex(3)> Process.exit(pid, :kill)
true
iex(4)> Process.alive?(pid)
false 
```

解决这个问题的一个办法是在产生进程时给它们命名，这样我们就可以通过名字来引用它们。每当一个命名的进程被一个超级用户重新启动时，新启动的进程自动接收它所替换的进程的名称。

```
iex(5)> {:ok, pid} = DynamicSupervisor.start_child(Hayago.GameSupervisor, {Hayago.Game, name: :game_1})
{:ok, #PID<0.285.0>}
iex(6)> Process.whereis(:game_1)
#PID<0.285.0>
iex(7)> Process.exit(pid, :kill)
true
iex(8)> Process.whereis(:game_1)
#PID<0.288.0>
iex(9)> GenServer.cast(pid, {:place, 0})
:ok 
```

在这里，我们开始一个新游戏，并使用`:game_1`作为它的名字。如果我们杀死了游戏进程，另一个进程会自动产生并取代它。新流程共享相同的名称，所以我们可以继续使用`:game_1`来引用新流程。

这样，我们可以在生成进程时为每个进程生成一个唯一的名称，并保存在我们的 socket 中，以便以后引用游戏。但是，等等！我们用原子来命名我们的过程。因为原子不是垃圾收集的，所以产生大量的游戏会耗尽我们的内存。取而代之的是，我们想使用字符串，字符串*被*垃圾收集。

因为我们不能用一个字符串来命名一个进程，我们需要使用一个[注册表](https://hexdocs.pm/elixir/master/Registry.html)来链接字符串名称和游戏 PID。Elixir 的 GenServer 实现有一种内置的方式，通过它的`:via`-元组来引用注册表中的进程。首先，每当应用程序启动时，让我们在主监控器中启动注册表。

```
# lib/hayago/application.ex
defmodule Hayago.Application do
  # ...

  def start(_type, _args) do
    children = [
      HayagoWeb.Endpoint,
      {Registry, keys: :unique, name: Hayago.GameRegistry},
      {DynamicSupervisor, strategy: :one_for_one, name: Hayago.GameSupervisor}
    ]

    opts = [strategy: :one_for_one, name: Hayago.Supervisor]
    Supervisor.start_link(children, opts)
  end

  # ...
end 
```

有了新的注册表，我们可以使用字符串作为名称来启动进程，并在以后引用它们，而不用为每个衍生的游戏创建一个 atom。

```
iex(1)> {:ok, pid} = DynamicSupervisor.start_child(Hayago.GameSupervisor, {Hayago.Game, name: {:via, Registry, {Hayago.GameRegistry, "game_1"}}})
{:ok, #PID<0.294.0>}
iex(2)> Process.exit(pid, :kill)
iex(3)> GenServer.cast({:via, Registry, {Hayago.GameRegistry, "game_1"}}, {:place, 0})
:ok 
```

## 切换到游戏流程

我们将更新`GameLive.mount/2`函数来启动一个保存游戏状态的监督进程，而不是创建一个新的游戏结构并将其直接分配给套接字。

```
# lib/hayago_web/live/game_live.ex
defmodule HayagoWeb.GameLive do
  # ...

  def mount(_session, socket) do
    name =
      ?a..?z
      |> Enum.take_random(6)
      |> List.to_string()

    {:ok, _pid} =
      DynamicSupervisor.start_child(Hayago.GameSupervisor, {Game, name: via_tuple(name)})

    {:ok, assign_game(socket, name)}
  end

  # ...

  defp via_tuple(name) do
    {:via, Registry, {Hayago.GameRegistry, name}}
  end

  defp assign_game(socket, name) do
    socket
    |> assign(name: name)
    |> assign_game()
  end

  defp assign_game(%{assigns: %{name: name}} = socket) do
    game = GenServer.call(via_tuple(name), :game)
    assign(socket, game: game, state: Game.state(game))
  end
end 
```

我们创建一个随机的六个字符的字符串作为我们流程的名称，当我们通过我们的`GameSupervisor`生成流程时使用它。`via_tuple/1`便利函数返回我们将需要在注册表中注册进程名称的`:via`-元组。

最后，我们添加一个`assign_game/1-2`函数，它接受游戏注册名称的一个套接字。它调用 GenServer 进程来获取游戏的当前状态，然后将其分配给套接字。

接下来，我们将切换两个`handle_event/3`版本，通过 GenServer 使用游戏。

```
# lib/hayago_web/live/game_live.ex
defmodule HayagoWeb.GameLive do
  # ...

  def handle_event("place", index, %{assigns: %{name: name}} = socket) do
    :ok = GenServer.cast(via_tuple(name), {:place, String.to_integer(index)})
    {:noreply, assign_game(socket)}
  end

  def handle_event("jump", destination, %{assigns: %{name: name}} = socket) do
    :ok = GenServer.cast(via_tuple(name), {:jump, String.to_integer(destination)})
    {:noreply, assign_game(socket)}
  end

  # ...
end 
```

同样，我们使用`via_tuple/1`函数将我们的`:place`和`:jump`函数直接投射到游戏进程中。然后，我们通过调用我们的`assign_game/1`函数将游戏重新分配给套接字。

## 多人 URL 共享

为了允许多个玩家连接到同一个游戏，我们将游戏的名称添加到第一个用户可以共享的 URL 中。当用户开始一个新游戏时，我们将使用 HTML5 历史 API 中的`pushState`函数将游戏名称添加到 URL 中，而无需重新加载页面。

我们将使用凤凰城 LiveView 为 that⁠—which 的`live_redirect/2`功能，这是我们最近开始游戏工作后添加的。为了确保我们的版本足够新，让我们在继续之前更新依赖项。

```
% mix deps.update phoenix_live_view 
```

当玩家访问 URL 中没有游戏名称的游戏时，应用程序将启动一个新的游戏，并更新 URL 以包括新创建的游戏名称。在我们的例子中，游戏是在我们的应用程序的根上提供的，所以访问 [http://localhost:4000](http://localhost:4000) 会重定向到 [http://localhost:4000？name=abcdef](http://localhost:4000?name=abcdef) ，其中“abcdef”是游戏名称。

为此，我们将用`handle_params/3`函数的两个变体替换`mount/2`函数。

```
# lib/hayago_web/live/game_live.ex
defmodule HayagoWeb.GameLive do
  # ...

  def handle_params(%{"name" => name} = _params, _uri, socket) do
    {:noreply, assign_game(socket, name)}
  end

  def handle_params(_params, _uri, socket) do
    name =
      ?a..?z
      |> Enum.take_random(6)
      |> List.to_string()

    {:ok, _pid} =
      DynamicSupervisor.start_child(Hayago.GameSupervisor, {Game, name: via_tuple(name)})

    {:ok,
    live_redirect(
      socket,
      to: HayagoWeb.Router.Helpers.live_path(socket, HayagoWeb.GameLive, name: name)
    )}
  end

  # ...
end 
```

第一个变体处理在 URL 参数中有名称的请求。在这种情况下，从与参数中的名称相对应的进程中获取最新的游戏状态，并使用`assign_game/2`函数将其分配给套接字。

第二个很像我们正在替换的`mount/2`函数。我们正在生成一个名字并开始一个游戏。但是，这个函数不是将游戏分配给套接字并返回，而是使用 LiveView 的`live_redirect/2`函数将名称添加到当前 URL 中。URL 更改后，第一个变体会自动执行，将名称和游戏状态分配给套接字。

## 广播移动到所有客户端

如果我们现在打开我们的游戏，我们会看到每次访问 [http://localhost:4000](http://localhost:4000) 都会被重定向到一个带有名称查询参数的 URL。打开该 URL 两次会将两个套接字连接到同一个游戏。

然而，当一块石头被放在一个窗口中时，它不会自动出现在另一个窗口中。只有刷新窗口后，我们才能看到石头的正确位置。

一切都连接正确，但是当第一个插座移动时，第二个插座没有得到通知。我们需要一种方法让所有连接的套接字订阅一个系统，该系统在一个移动时向所有客户机发布更新。

我们将使用`Phoenix.PubSub`来实现这一点。回到我们的`GameLive`模块，我们将在游戏创建时为每个套接字连接订阅一个通道，然后每当我们放置一块石头或在历史上旅行时，我们将通过该通道广播一条消息。

```
# lib/hayago_web/live/game_live.ex
defmodule HayagoWeb.GameLive do
  # ...

  def handle_params(%{"name" => name} = _params, _uri, socket) do
    :ok = Phoenix.PubSub.subscribe(Hayago.PubSub, name)
    {:noreply, assign_game(socket, name)}
  end

  # ...

  def handle_event("place", index, %{assigns: %{name: name}} = socket) do
    :ok = GenServer.cast(via_tuple(name), {:place, String.to_integer(index)})
    :ok = Phoenix.PubSub.broadcast(Hayago.PubSub, name, :update)
    {:noreply, assign_game(socket)}
  end

  def handle_event("jump", destination, %{assigns: %{name: name}} = socket) do
    :ok = GenServer.cast(via_tuple(name), {:jump, String.to_integer(destination)})
    :ok = Phoenix.PubSub.broadcast(Hayago.PubSub, name, :update)
    {:noreply, assign_game(socket)}
  end

  def handle_info(:update, socket) do
    {:noreply, assign_game(socket)}
  end

  # ...
end 
```

在`handle_params/3`(匹配带有名称查询参数的 URL 的变体)中，我们调用`Phoenix.PubSub.subscribe/2`。我们用游戏的名字作为频道的主题。因为所有连接的客户端都会点击这个函数，所以我们知道它们都会订阅这个主题。

在两个`handle_event/3`函数中，我们使用`Phoenix.PubSub.broadcast/3`向主题中的所有订阅者发送消息。我们将`:update`作为消息发送，然后在新添加的`handle_info/2`函数中获取该消息，该函数获取当前游戏的状态，并在收到更新消息时更新视图。

为了确保游戏被清理，我们需要在它们不再被使用时终止它们的进程。现在，我们将为 GenServer 的回调函数添加一个超时。当一个游戏十分钟没有任何交互时，进程会给自己发送一个`:timeout`消息来停止进程。

```
# lib/hayago/game.ex
defmodule Hayago.Game do
  # ...
  use GenServer, restart: :transient

  @timeout 600_000

  def start_link(options) do
    GenServer.start_link(__MODULE__, %Game{}, options)
  end

  @impl true
  def init(game) do
    {:ok, game, @timeout}
  end

  @impl true
  def handle_call(:game, _from, game) do
    {:reply, game, game, @timeout}
  end

  @impl true
  def handle_cast({:place, position}, game) do
    {:noreply, Game.place(game, position), @timeout}
  end

  @impl true
  def handle_cast({:jump, destination}, game) do
    {:noreply, Game.jump(game, destination), @timeout}
  end

  @impl true
  def handle_info(:timeout, game) do
    {:stop, :normal, game}
  end

  # ...
end 
```

通过向每个回调的响应元组添加一个以毫秒为单位的超时，我们告诉 GenServer 在进程十分钟没有收到消息时给自己发送一个超时消息。`:timeout`回调返回一个`:stop` -tuple 来告诉进程停止。

我们还确保在包含 GenServer 代码时将`:restart`的值设置为`:transient`，以确保管理程序只在游戏异常终止时重新启动游戏。

## 接下来是什么？

这就结束了在凤凰城实现围棋游戏的第三部分。在实现真正的多人游戏方面，我们已经走了很长的路，并且我们已经了解了 Elixir 的动态管理器、注册表和`Phoenix.PubSub`。

在未来的一集中，我们将继续把我们的游戏变成一个多人游戏，通过在棋盘上给每个连接分配一种颜色的石头，把每个连接分配为一个单独的玩家。到时候见！