# 在 Elixir 中构建围棋游戏:时间旅行和 Ko 规则

> 原文：<https://dev.to/appsignal/building-the-go-game-in-elixir-time-travel-and-the-ko-rule-4pei>

欢迎回到炼丹术！两周前，我们开始了我们的冒险[使用 Phoenix LiveView](https://blog.appsignal.com/2019/06/18/elixir-alchemy-building-go-with-phoenix-live-view.html) 在 Elixir 中实现围棋游戏。今天，我们回到游戏中来添加撤销和重做移动的能力，然后我们将实现围棋的 [ko](https://senseis.xmp.net/?Ko) 规则。

## 向前冲！

在本文中，我们将主要关注游戏的实现，但仍然会顺便了解一下 Phoenix LiveView。今天文章的[最终结果](https://hayago.herokuapp.com)包括撤销和重做按钮，游戏阻止你做出将游戏恢复到先前状态的动作。

[新的入门应用](https://github.com/jeffkreeftmeijer/hayago/tree/starter-2)包含了上一集的结果，这是一个使用 LiveView 渲染和更新棋盘的 Phoenix 应用。它在它的`State`模块中实现了围棋的一些规则，并跟踪每个棋手获得的棋子。这一次，我们将添加一个`Game`模块，保存整个游戏的状态，包括之前的移动。

喜欢直接跳到前面阅读代码吗？主分支包括了我们在这一集中要做的所有改变，以及测试覆盖和所有功能的完整文档。

## 时间旅行准备工作

在我们进行时间旅行并让玩家撤销和重做移动之前，游戏需要保存自开始以来的移动历史。目前，它保留了一个`State`结构，可以在模板中作为`@state`访问。

每当玩家通过点击棋盘上的一个不可见按钮来移动时，`GameLive`模块就会处理该事件。它使用`State.place/2`创建一个新的状态结构，并将其指定为新的状态来更新视图。

```
# lib/hayago_web/live/game_live.ex
defmodule HayagoWeb.GameLive do
  # ...

  def handle_event("place", index, %{assigns: assigns} = socket) do
    new_game = Game.place(assigns.game, String.to_integer(index))
    {:noreply, assign(socket, game: new_game, state: Game.state(new_game))}
  end
end 
```

由于这种实现，目前没有办法跳回历史，因为我们的游戏在每次移动时都会替换状态，并忘记之前的状态。

为了保留历史，我们将添加一个名为`Game`的结构，在其`:history`属性中保存状态的历史。当游戏开始时，它在它的历史列表中有一个单一的空状态来代表空棋盘。

```
# lib/hayago/game.ex
defmodule Hayago.Game do
  alias Hayago.{Game, State}
  defstruct history: [%State{}]

  # ...
end 
```

我们将添加一个方便的函数来获取游戏的当前状态。历史列表中的第一个状态总是当前状态，所以我们可以用列表的第一个元素来获取当前状态。

```
# lib/hayago/game.ex
defmodule Hayago.Game do
  # ...

  def state(%Game{history: [state | _]}) do
    state
  end

  # ...
end 
```

最后，我们将为`Game`模块实现一个`place/2`函数。它使用`State.place/2`来创建一个新的状态结构，然后将其添加到历史列表中。

```
# lib/hayago/game.ex
defmodule Hayago.Game do
  # ...

  def place(%Game{history: [state | _] = history} = game, position) do
    %{game | history: [State.place(state, position) | history]}
  end
end 
```

现在，让我们在`GameLive`模块中使用新的游戏结构。`mount/2`功能用于设置游戏。我们将创建一个新的游戏结构并将其分配给`:game`赋值函数，而不是创建一个状态结构并将其直接分配给套接字。

我们仍将在模板中使用 state struct，因此我们将通过调用新的`Game.state/1`函数并将结果指定为`:state`来在实时视图中预加载它。

```
# lib/hayago_web/live/game_live.ex
defmodule HayagoWeb.GameLive do
  alias Hayago.Game
  use Phoenix.LiveView

  # ...

  def mount(_session, socket) do
    game = %Game{}
    {:ok, assign(socket, game: game, state: Game.state(game))}
  end

  # ...
end 
```

放置石头时，我们现在将调用`Game.place/2`函数来更新当前状态，同时保留历史列表。同样，我们在更新的游戏上使用`Game.state/1`来预加载状态。

```
# lib/hayago_web/live/game_live.ex
defmodule HayagoWeb.GameLive do
  alias Hayago.Game
  use Phoenix.LiveView

  # ...

  def handle_event("place", index, %{assigns: assigns} = socket) do
    new_game = Game.place(assigns.game, String.to_integer(index))
    {:noreply, assign(socket, game: new_game, state: Game.state(new_game))}
  end
end 
```

如果我们再次尝试这个游戏，我们不会看到任何变化。虽然我们保留了所有移动的历史，但是我们还没有对它做任何事情。

## 时间旅行

尽管我们现在保留了游戏状态的历史，我们的`Game`模块中的所有函数仍然只使用列表中的第一个状态。由于每次移动都会在历史列表中添加一个状态，因此列表中的第一个总是最新的状态。

为了允许向后和向前跳转，我们将向`Game`结构添加一个名为`:index`的属性，默认为 0。

```
# lib/hayago/game.ex
defmodule Hayago.Game do
  alias Hayago.{Game, State}
  defstruct history: [%State{}], index: 0

  # ...
end 
```

有了索引，我们可以跳回到以前的状态，而不必从列表的前面删除状态。相反，我们将更新我们的`state/1`函数来考虑索引。它不是返回列表中的第一个状态，而是使用游戏的索引来查找列表中的当前状态。

```
# lib/hayago/game.ex
defmodule Hayago.Game do
  # ...

  def state(%Game{history: history, index: index}) do
    Enum.at(history, index)
  end

  # ...
end 
```

现在，如果我们有一个具有三个状态历史的游戏，将`:index`属性设置为 1 将返回列表中的第二个状态，本质上是恢复到那个状态。

为了跳转到不同的索引，我们将添加一个名为`jump/2`的便利函数，它覆盖了`Game`结构的`:index`属性。

```
defmodule Hayago.Game do
  # ...

  def jump(game, destination) do
    %{game | index: destination}
  end
end 
```

现在我们的游戏模块保存了移动的历史，我们可以在它们之间跳转，让我们添加按钮来在玩游戏时撤销和重做移动。

```
# lib/hayago_web/templates/game/index.html.leex
# ...

<div class="history">
  <button phx-click="jump" phx-value="<%= @game.index + 1%>">Undo</button>
  <button phx-click="jump" phx-value="<%= @game.index - 1%>">Redo</button>
</div> 
```

我们使用“jump”作为两个按钮的`phx-click`属性的值，这是我们将在`GameLive`模块中实现的一个函数的名称。

`phx-value`属性用于传递我们想要跳转到的历史索引。历史列表会反转，因为新的移动会优先于历史列表。因此，要撤消移动，我们将当前索引增加 1，然后减少 1 以重做。

在`GameLive`模块中，我们将通过用当前游戏和传递的索引调用`Game.jump/2`来处理跳转事件，给我们一个新的游戏结构，我们将在套接字上分配它。像以前一样，为了方便起见，我们更新了状态赋值。

```
# lib/hayago_web/live/game_live.ex
defmodule HayagoWeb.GameLive do
  # ...

  def handle_event("jump", destination, %{assigns: %{game: game}} = socket) do
    new_game = Game.jump(game, String.to_integer(destination))
    {:noreply, assign(socket, game: new_game, state: Game.state(new_game))}
  end
end 
```

如果我们打开浏览器并导航到 [https://localhost:4000](https://localhost:4000) ，我们可以看到撤销和重做按钮起作用了。在放置了一些石头后，我们可以点击撤销按钮来移除最后一块石头，点击重做按钮来再次移除最后一块石头。

## 历史上的分支

然而，如果我们取消一个移动并尝试放置一块石头，我们会注意到新的石头没有被添加到棋盘上。相反，我们刚刚按下撤销按钮移除的石头会重新出现。

事实证明，在按下撤销按钮后放置新石头之前，我们还有一个步骤要做。让我们来分析一下发生了什么。

1.  我们在棋盘上放置一块石头，它会在历史列表中添加一个新的状态。
2.  我们按下撤销按钮，将历史索引增加到 1，使我们回到初始状态，再次给我们一个空的板。
3.  我们尝试放置一个新的石头，它会在历史列表中添加一个新的状态。游戏的指数保持在 1。

新状态是列表中的第一个，意味着它的索引是 0，但是游戏索引仍然是 1。该索引属于我们通过按“撤消”按钮撤消的移动。本质上，我们新加入的石头被延迟了一步。

为了解决这个问题，每当我们通过放置一个新的石头来添加一个新的状态时，我们需要从列表中删除所有未完成的状态。从历史中删除状态允许用户在撤销移动后分支到不同的方向。

```
# lib/hayago/game.ex
defmodule Hayago.Game do
  # ...

  def place(%Game{history: history, index: index} = game, position) do
    new_state =
      game
      |> Game.state()
      |> State.place(position)

    %{game | history: [new_state | Enum.slice(history, index..-1)], index: 0}
  end

  # ...
end 
```

在新版本的`place/2`函数中，我们使用`Enum.slice/2`从历史列表中删除未完成的动作。我们还会将游戏的索引属性重置为 0，这确保了新添加的石头总是立即出现。现在时间旅行可行了，尽管我们不确定这是否意味着我们需要担心避免[宿命悖论](https://www.quirkbooks.com/post/classic-time-travel-paradoxes-and-how-avoid-them)。

## 禁用不适用的撤销和重做按钮

现在，当没有要撤销或重做的动作时，我们需要确保撤销和重做按钮被禁用。为此，我们将实现一个名为`history?/2`的函数，它接受一个游戏结构和一个索引，并返回该索引是否是游戏历史的一部分。

```
# lib/hayago/game.ex
defmodule Hayago.Game do
  # ...

  def history?(%Game{history: history}, index) when index >= 0 and length(history) > index do
    true
  end

  def history?(_game, _index), do: false
end 
```

我们的函数检查请求的索引是否大于 0，以确保游戏不允许在还没有完成任何移动时向前跳跃。然后，它检查历史列表的长度是否大于索引，以确保我们在撤销移动时不会超出列表。

有了我们的新函数，我们可以检查游戏是否可以在呈现按钮之前撤销和重做上一次和下一次移动。如果不能，它将呈现一个禁用的版本。

```
# lib/hayago_web/templates/game/index.html.leex
<div class="history">
  <%= if Hayago.Game.history?(@game, @game.index + 1) do %>
    <button phx-click="jump" phx-value="<%= @game.index + 1%>">Undo</button>
  <% else %>
    <button disabled="disabled">Undo</button>
  <% end %>

  <%= if Hayago.Game.history?(@game, @game.index - 1) do %>
    <button phx-click="jump" phx-value="<%= @game.index - 1%>">Redo</button>
  <% else %>
    <button disabled="disabled">Redo</button>
  <% end %>
</div> 
```

再次尝试这个游戏，我们会发现除了之前的移动列表之外，我们无法撤销更多的移动，也无法重做到未来。

## Ko 法则

除了允许玩家撤销和重做移动，保存历史允许我们实现围棋的 ko 规则。

> 如果一个玩法会产生(在该玩法的所有步骤完成后)创建一个先前在游戏中已经出现的位置的效果，则该玩法是非法的。

Go 阻止玩家将棋盘恢复到先前的状态。在实践中，当一个玩家抓住一颗石头，而另一个玩家采取行动立即抓住新添加的石头时，就会发生这种情况。

目前，我们在`State.legal?/2`函数中验证移动，该函数检查一个位置是否是空的，并确保在该位置放置石头是自由的。

为了实现 ko 规则，我们需要游戏状态的历史，这意味着我们需要访问`Game`结构。为此，我们添加了一个名为`Game.legal?/2`的函数。

```
# lib/hayago/game.ex
defmodule Hayago.Game do
  # ...

  def legal?(game, position) do
    State.legal?(Game.state(game), position) and not repeated_state?(game, position)
  end

  defp repeated_state?(game, position) do
    %Game{history: [%State{positions: tentative_positions} | history]} =
      Game.place(game, position)

    Enum.any?(history, fn %State{positions: positions} ->
      positions == tentative_positions
    end)
  end

  # ...
end 
```

我们的新函数将游戏结构作为第一个参数，新石头的位置作为第二个参数。它调用`State.legal?/2`来确保已经实现的规则得到满足。然后，它使用`repeated_state?/2`来确保新状态还没有发生，这是一个私有函数，它放置石头并将新状态与历史列表进行比较。

最后，我们将更新模板，从直接使用`State.legal?/2`切换到使用`Game.legal?/2`，这将游戏历史考虑在内。

```
# lib/hayago_web/templates/game/index.html.leex
# ...

<div class="board <%= @state.current %>">
  <%= for {value, index} <- Enum.with_index(@state.positions) do %>
    <%= if Hayago.Game.legal?(@game, index) do %>
      <button phx-click="place" phx-value="<%= index %>" class="<%= value %>"></button>
    <% else %>
      <button class="<%= value %>" disabled="disabled"></button>
    <% end %>
  <% end %>
</div>

# ... 
```

回到游戏中，我们将会看到我们不能再采取行动将游戏的状态恢复到历史中的状态。

## 时间旅行和 ko 法则

我们已经通过增加一个聪明的方法来恢复移动来改进我们的游戏，通过这个，我们能够实现 Ko 规则。一路走来，我们已经了解到 LiveView 是多么灵活，因为我们几乎没有接触过 live view 代码，尽管我们在游戏中做了相当多的改变。

这是我们用 Elixir 和 Phoenix LiveView 实现围棋游戏系列的第二部分。我们很想知道到目前为止你是如何享受它的，以及接下来你想了解什么。

如果你想让这篇文章一出现就在你的收件箱里，回到过去，然后[订阅长生不老药列表](https://blog.appsignal.com/elixir-alchemy)。直到下一次，同时，小心你新发现的时间旅行能力！