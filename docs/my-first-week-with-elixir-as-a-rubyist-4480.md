# 我作为 Rubyist 的第一周

> 原文：<https://dev.to/ssolo112/my-first-week-with-elixir-as-a-rubyist-4480>

我最近开始为一个即将到来的项目学习长生不老药。作为我平时准备工作的一部分，我已经学习了[语言文档](https://elixir-lang.org/)，走过了[考恩](https://github.com/elixirkoans/elixir-koans)，并在[exercisem . io](https://exercism.io)上练习了一些形。

最终的结果是我解决问题的方法的一些改变，我想和你分享。

在我们开始之前，您可能会发现一些令人困惑的事情:

1)函数签名包括它们采用的参数数量，称为 arity。函数签名`any?(collection, fn)`将被称为`any?/2`。

2)未使用的参数被命名，但以下划线为前缀。

3) `iex`是 REPL 在仙丹中的名字。

# 模式匹配与分解

模式匹配作为一个一流的想法对我来说是新的。通常我会避免 case 语句和析构，而倾向于多态，但是我已经开始尝试一些可能的有趣案例。

让我们看一个例子。在这里，我将变量`a`匹配到一个字符串的模式。

```
iex> a = "Some String" # Binds string to a
"Some String"
iex> "Some String" = a # Matches a to the pattern
"Some String" 
```

Enter fullscreen mode Exit fullscreen mode

当不匹配时，模式匹配会引发错误。

```
iex> "Not a Match" = a # Throws an error
** (MatchError) no match of right hand side value: "Some String" 
```

Enter fullscreen mode Exit fullscreen mode

使用模式匹配可以析构列表。在这里，您可以看到如何将列表的第一个元素与其余元素分开。

```
iex> [head | tail] = [1, 2, 3, 4, 5]
iex> head
1
iex> tail
[2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

您还可以析构更复杂的数据类型。例如，从地图中取出一把钥匙:

```
iex> user = %{id: 1, login: %{user_name: 'bob114', password: 'Apple12Sauce!'}}
iex> %{id: id} = user
iex> id
1
iex> %{login: %{user_name: user_name}} = user
iex> user_name
bob114 
```

Enter fullscreen mode Exit fullscreen mode

# 先递归

我不得不改变我的风格来接受递归。我最后一次写这么多递归代码是在计算机科学课程上。这是一个巨大的风格变化，但最好通过一个导航集合的例子来演示。

让我们创建一个函数来确定集合中是否包含任何元素，并将该函数命名为`any?/2`。

我首先问，“什么是`any?/2`的基本情况？”。

当列表参数为空时，就会出现这种情况。使用模式匹配，我们可以定义一个函数头，它将匹配第一个传递的元素是一个空列表的场景，并返回`false`。

```
defmodule Example do
  def any?([], _a), do: false # Matches when the first param is an empty list
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来我想问，“递归的情况是什么？”

当列表不为空时，我会尝试将问题分成更小的部分，这样我就可以触及基本案例。有两种可能，1)第一个元素等于变量，2)不等于变量，我们需要继续寻找。如果我们实现场景一，那么我们的`head`等于`a`，所以我们返回 true。

```
defmodule Example do
  def any?([], _a), do: false
  def any?([head | _tail], a) do
    if head == a do
      true
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在最后一个场景中，我们处理列表其余部分的递归。

```
defmodule Example do
  def any?([], _a), do: false
  def any?([head | tail], a) do
    if head == a do
      true
    else 
      any?(tail, a) 
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

# 维持状态

酏剂状态是在进程内部管理的。每个进程都使用递归来模拟状态转换。流程允许其他流程通过消息更改或查询它们的状态。与其向您展示低级工具(`spawn`、`Agent`或`Task`)，我所知道的最高级别的构造是`GenServer`。

`GenServer`是一个核心语言结构，它将发送和接收消息的过程的所有复杂性都包了起来。它是一个模块，有两种类型的功能，异步和同步。

同步函数是`call` s，它们会用一些数据回复调用进程。他们通过返回一个类似于`{:reply, data, state}`的元组来实现这一点。`:reply`告诉服务器响应调用者，数据是回复中要发送的内容，状态是流程的新状态。

```
defmodule OurServer do
  use GenServer

  ...

  def call({:message, caller_data}, _from, state) do
    {:reply, caller_data, [caller_data]}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

异步函数是不响应调用者的。他们返回一个类似于`{:noreply, state}`的元组。`:noreply`告诉服务器不要回复，而`state`是流程的新状态。

```
defmodule OurServer do
  use GenServer

  def cast({:message, caller_data}, state) do
    {:noreply, caller_data}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在以后的文章中，我计划更深入地讨论`GenServer` s，但目前就这些。

# 结论

我第一次尝试使用 Elixir，冒险进入函数式语言的领域。

我发现第一周最有趣的事情是:

*   模式匹配
*   递归优先
*   用流程模拟状态

如果你喜欢这篇文章，并想了解更多关于仙丹的内容，请在下面留下你的评论或赞。谢谢！