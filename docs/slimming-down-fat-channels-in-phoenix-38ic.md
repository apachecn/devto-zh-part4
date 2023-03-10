# 凤凰城的减肥渠道

> 原文：<https://dev.to/jmbejar/slimming-down-fat-channels-in-phoenix-38ic>

*原载于 WyeWorks 博客:[https://www . WyeWorks . com/blog/2019/07/19/slimping-down-fat-channels-in-phoenix/](https://www.wyeworks.com/blog/2019/07/19/slimming-down-fat-channels-in-phoenix/)*

在这篇文章中，我们将讨论一个我们在 Phoenix 项目中应用的方法。考虑到应用程序包括实时协作功能，通道是解决方案的重要组成部分。

我们有一个特定的通道模块，它具有越来越多的消息处理器功能。就设计而言，我们认为让所有的交互都由一个通道模块来管理仍然是有意义的。然而，我们开始注意到模块本身正在失去控制，有许多不相关的逻辑和大量的代码行。出于这个原因，我们开始考虑如何将代码拆分到不同的模块，但仍然只使用一个 Phoenix 通道。

## 问题

假设我们想要实现一个多步向导界面，在每一步中都有某种协作交互。这意味着在一个给定的时刻，团队中的每个人都将在同一个步骤上工作。下面是我们最初是如何实现这个向导的。

```
defmodule MyExampleAppWeb.WizardChannel do
  use Phoenix.Channel

  def join("wizard:" <> id, _message, socket) do
    ...
  end

  # The message that begins the multi-step experience
  def handle_in("start_first_step", body, socket) do
    ...
  end

  # This message is valid in step 1
  def handle_in("send_info", body, socket) do
    ...
  end

  # This message is valid in step 1 but takes the group into step 2
  def handle_in("start_second_step", body, socket) do
    ...
  end

  # This message is valid in step 2
  def handle_in("pick_option", body, socket) do
    ...
  end
end 
```

您可能已经注意到，这种方法的伸缩性不好。随着步骤中更多的操作和更多的步骤本身被添加到向导中，通道模块变得越来越复杂。

## 拆分不同模块中的逻辑

为每个步骤创建特定的模块可能是解决我们问题的最自然的方法，所以我们尝试了:

```
defmodule MyExampleAppWeb.WizardChannel do
  use Phoenix.Channel

  alias MyExampleAppWeb.WizardChannel.{FirstStep, SecondStep}
  ...

  def handle_in("start_first_step", body, socket) do
    FirstStep.start(data, socket)
  end

  def handle_in("send_info", body, socket) do
    FirstStep.send_info(data, socket)
  end

  ...
end

defmodule MyExampleAppWeb.WizardChannel.FirstStep do
  def start(body, socket) do
    ...
    broadcast(socket, "first_step_started", ...)
    {:noreply, socket}
  end

  def send_info(body, socket) do
    ...
    broadcast(socket, "info_received", ...)
    {:noreply, socket}
  end
end 
```

很简单。我们只是将逻辑移至专用模块，同时添加一些从通道模块委托的琐碎代码。嗯，有一个问题。此解决方案无法编译🙈！

我们遇到了在`FirstStep`模块上下文中找不到的`broadcast/3`函数的问题。如果我们使用 [Phoenix 提供的任何可用功能，也会发生同样的情况。频道](https://hexdocs.pm/phoenix/Phoenix.Channel.html)像`push/3`、`reply/2`等等。

## 寻找缺失的功能

所有这些特定于通道的功能都可以在我们的`WizardChannel`中找到，因为我们有这样一行:

```
use Phoenix.Channel 
```

我们不能简单地在我们的辅助模块中做同样的事情，比如`MyExampleAppWeb.WizardChannel.FirstStep`，因为这一行不仅仅是导入一堆函数:它定义了一个将在运行时产生的进程，并负责处理在我们的 websocket 连接中来回传递的所有消息。

解决方法很简单。我们可以直接导入`Phoenix.Channel`模块中定义的必要函数。对这些函数的访问没有障碍，它们是框架的公共 API 的一部分(尽管在官方文档中更容易找到完整使用`Phoenix.Channel`模块的例子)

我们的`FirstStep`的工作解决方案如下:

```
defmodule MyExampleAppWeb.WizardChannel.FirstStep do

  #### Updated line ####
  import Phoenix.Channel, only: [broadcast: 3]

  def start(body, socket) do
    ...
    broadcast(socket, "first_step_started", ...)
    {:noreply, socket}
  end

  def send_info(body, socket) do
    ...
    broadcast(socket, "info_received", ...)
    {:noreply, socket}
  end
end 
```

## 新的 DSL 诞生了

让我们看一下我们的`WizardChannel`模块在添加了几个步骤和处理函数之后的样子:

```
defmodule MyExampleAppWeb.WizardChannel do
  use Phoenix.Channel

  alias MyExampleAppWeb.WizardChannel.{FirstStep, SecondStep, LastStep}

  ...

  # First step messages
  def handle_in("start_first_step", body, socket), do: FirstStep.start(body, socket)
  def handle_in("send_info", body, socket), do: FirstStep.send_info(body, socket)

  # Second step messages
  def handle_in("start_second_step", body, socket), do: SecondStep.start(body, socket)
  def handle_in("pick_option", body, socket), do: SecondStep.pick_option(body, socket)
  def handle_in("notify_option", body, socket), do: SecondStep.notify_option(body, socket)

  ...

  # Last step messages
  def handle_in("confirm", body, socket), do: LastStep.confirm(body, socket)
  def handle_in("end_wizard", body, socket), do: LastStep.end_wizard(body, socket)

end 
```

我们注意到代码大部分是重复的，除了定义适当的模块和功能之外，没有太多的逻辑。此外，我们对函数进行了分组，并添加了引用不同步骤的注释行，这样文件更容易导航。

在这里，我们开始看到一个实现定制 DSL 的机会，试图拥有一段更易读、更易维护的代码。Elixir 及其元编程能力使得用几行代码构建 DSL 成为可能(尽管理解这些代码需要了解宏在 Elixir 中是如何工作的)。

让我们看看这个模块在实现了这个新想法之后是什么样子的:

```
defmodule MyExampleAppWeb.WizardChannel do
  use Phoenix.Channel
  use MyExampleAppWeb.WizardChannel.MessagesHandler

  alias MyExampleAppWeb.WizardChannel.{FirstStep, SecondStep, LastStep}

  ...

  handle_step_messages [:start_first_step, :send_info], with_module: FirstStep
  handle_step_messages [:start_second_step, :pick_option, :notify_option], with_module: SecondStep
  handle_step_messages [:confirm, :end_wizard], with_module: LastStep
end 
```

很多重复都没了！我们很高兴看到这段代码更好地传达了意图(当然，假设您对我们的定制 DSL 有一定的了解)。

它是如何工作的？请注意，我们在本模块中添加了以下内容:

```
use MyExampleAppWeb.WizardChannel.MessagesHandler 
```

让我们看看`WizardChannel.MessagesHandler`模块代码，探究一下`handle_step_messages`宏是如何实现的。

```
defmodule MyExampleAppWeb.WizardChannel.MessagesHandler do
  defmacro __using__(_opts) do
    quote do
      import unquote(__MODULE__)
    end
  end

  defmacro handle_step_messages(messages, with_module: module) do
    quote bind_quoted: [messages: messages, module: module] do
      Enum.each(messages, fn message ->
        def handle_in(unquote(Atom.to_string(message)), data, socket) do
          apply(unquote(module), unquote(message), [data, socket])
        end
      end)
    end
  end
end 
```

使用`use`指令时，调用`__using__`宏。我们使用这个特殊的宏只是为了确保这个模块中的所有函数和宏都可以在我们的主模块中使用(在我们的例子中是`MyExampleAppWeb.WizardChannel`)。

Elixir 宏用于以编程方式生成在编译时调用宏的地方注入的代码。我们现在有了一个宏，它可以生成我们以前手写的所有函数。它接收一个原子列表，其中包含消息和模块的名称，在该模块中为每个特定的向导步骤实现定制逻辑。

我们在这里使用了一些惯例。不同模块中的功能等同于消息名称。例如，消息类型`:send_info`将由函数`FirstStep.send_info/2`处理。此外，我们假设这些函数有固定的 arity，接收消息体和`Phoenix.Socket`结构。

因此，我们正在遍历消息类型列表，并为每种类型生成一个函数定义。每个生成函数的主体如下

```
apply(unquote(module), unquote(message), [data, socket]) 
```

也就是靠`Kernel.apply/3`。从指定的模块中动态调用正确的函数是可能的，因为消息名现在是作为变量而不是文字传递的。

本文并不打算全面解释这个解决方案的元编程方面。如果像`quotes`和`unquotes`这样的事情仍然困扰着你，我强烈推荐[阅读长生不老药指南](https://elixir-lang.org/getting-started/meta/quote-and-unquote.html)中的相关文档。

## 结果

在实现了这个 DSL 之后，我们对这部分代码感觉好多了。我们最终找到了一种体面的方式来将通道功能划分到不同的模块中，同时也找到了一种清晰的方式来使用我们的`handle_step_messages/2`宏在通道模块中编写所有的粘合代码。

这种方法带来了新的机遇和挑战。例如，我们已经发展了所提出的解决方案，以支持具有不同 arity 的函数(一些函数没有利用`Phoenix.Socket` struct 参数)。此外，我们现在正在探索一些根据目标模块运行共享验证的方法。无论如何，过度使用元编程方法可能会导致过度工程化的解决方案，对于后来处理代码的其他开发人员来说很难理解，所以很明显我们需要在代码的简洁性和简单性之间取得平衡。

胖凤凰频道有没有发现类似的问题？你是怎么做到的？如果您尝试过不同的解决方案，或者您认为我们的故事有用，请发表评论。

快乐编码与药剂和凤凰‍👩🏽‍💻👨🏻‍💻！

## 鸣谢

非常感谢尼古拉斯·费拉罗和 T2 帮助撰写这篇文章。他们都参与了所描述的解决方案的实现。