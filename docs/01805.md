# 在药剂中使用 Faktory

> 原文：<https://dev.to/swelham/up-and-running-with-faktory-in-elixir-16gl>

Faktory 是一个服务器守护进程，它允许我们对希望在应用程序后台执行的作业进行排队。它还处理作业处理的其他方面，如失败、重试和队列优先级。在本文中，我们将看看如何使用 [faktory_worker](https://github.com/SeatedInc/faktory_worker) 库从 Elixir 应用程序连接到 faktory 并处理作业。

在开始之前，让我们快速总结一下我们可以用这个库做什么。

Faktory Worker 允许我们执行任何类型的工作，无论是发送电子邮件、接受客户付款还是调整图像大小。我们在 Elixir 中执行的任何任务都可以通过 Faktory 在后台运行。通过使用这个库，我们可以处理与 Faktory 的所有通信。这意味着我们可以专注于编写完成工作的代码，而不用担心复杂的错误处理和重试逻辑。

让我们从建立一个 Faktory 服务器开始。

## 开始制造

有几种方法可以让 Faktory 为当地发展服务。我最喜欢的方法是使用 Docker，所以让我们从那里开始吧。

要让 Faktory 运行，只需使用下面的命令。

```
$ docker run --rm -it -p 7419:7419 -p 7420:7420 contribsys/faktory:latest 
```

一旦 Docker 启动了 Faktory 服务器，您就可以访问 [http://localhost:7420](http://localhost:7420) 来查看仪表板并确认服务器已经正确启动。

我发现这种方法是使用 Faktory 进行本地开发的最快、最灵活的方式。这使我们可以轻松地运行映射到不同端口的多个服务器，允许每个新项目使用它自己的 Faktory 实例。

或者，如果你在 macOS 上，Faktory 也可以通过 homebrew 安装，如果你在 Linux 上，有二进制包可用。更多说明可在项目的[安装页面](https://github.com/contribsys/faktory/wiki/Installation)中找到。

## 我们有 Faktory 跑步，接下来呢？

我们首先需要的是长生不老药。您可以使用您已经拥有的现有项目，并在需要的地方应用以下步骤，或者您可以从头开始并跟随。

第一步是创建我们的应用程序。

```
$ mix new example_app 
```

接下来，我们需要将`faktory_worker`依赖项添加到我们的`mix.exs`文件中。

```
defp deps do
  [
    {:faktory_worker, "~> 1.0"}
  ]
end 
```

有了这些，我们现在就可以配置应用程序了。启动 Faktory Worker 的推荐方法是将其包含在监督树中。

让我们添加一个新的应用程序模块来处理我们的应用程序如何启动，并将`FaktoryWorker`模块添加到其子模块列表中。

```
defmodule ExampleApp.Application do
  use Application

  def start(_, _) do
    children = [
      FaktoryWorker
    ]

    Supervisor.start_link(children,
      strategy: :one_for_one,
      name: ExampleApp.Supervisor
    )
  end
end 
```

一旦我们准备好了，我们需要更新我们的`mix.exs`文件的`application`部分，以确保我们的应用程序启动。

```
def application do
  [
    mod: {ExampleApp.Application, []},
    extra_applications: [:logger]
  ]
end 
```

现在回到终端，运行`iex -S mix`命令启动应用程序。

如果您看一下我们之前查看的 Faktory 仪表板，您会看到页面底部报告了几个连接。这证实了我们的应用程序已经使用默认配置成功启动并连接到 Faktory。

## 让我们执行一些工作吧！

在执行我们的第一项工作之前，我们需要建立一个 worker 模块来负责这项工作。

工作模块有两个要求。它必须引入 Faktory Worker 作业功能，并定义一个名为`perform`的函数，用于处理传入的作业。

让我们从创建最简单的工人`HelloWorldWorker`开始。

```
defmodule ExampleApp.HelloWorldWorker do
  use FaktoryWorker.Job

  def perform(name) do
    IO.puts("Hello #{name}!")
  end
end 
```

这里我们使用了`FaktoryWorker.Job`模块，它引入了发送和获取作业所需的功能。然后我们定义了`perform/1`函数，当 Faktory Worker 接收到要执行的工作时，它会将作业参数传递给这个函数。

要将一个任务发送到 Faktory，我们可以调用由`FaktoryWorker.Job`模块添加到我们的 worker 中的`perform_async/1`函数。

让我们打开一个 iex 会话并打个招呼。

```
iex(1)> ExampleApp.HelloWorldWorker.perform_async("Stuart")
Hello Stuart
:ok 
```

太好了，我们刚刚向 Faktory 发送了一个任务，取回并执行了一些工作。如果您再看一下 Faktory 仪表板，您会看到它现在报告已经处理了 1 个作业。

这是一个简单的例子，让我们尝试一些稍微有趣的东西。

到目前为止，我们只完成了接受二进制输入的工作。Faktory Worker 支持 JSON 可序列化的任何数据类型，例如 map。让我们创建另一个允许我们进行一些基本计算的 worker。

```
defmodule ExampleApp.MathWorker do
  use FaktoryWorker.Job

  def perform(%{"op" => "+"} = params) do
    log(params["x"] + params["y"])
  end

  def perform(%{"op" => "-"} = params) do
    log(params["x"] - params["y"])
  end

  defp log(result), do: IO.puts("The answer is: #{result}")
end 
```

现在回到 iex，我们可以计算一些数字。

```
iex(1)> ExampleApp.MathWorker.perform_async(%{op: "+", x: 3, y: 5})
The answer is: 8
:ok
iex(2)> ExampleApp.MathWorker.perform_async(%{op: "-", x: 10, y: 6})
The answer is: 4
:ok 
```

在这里，我们可以看到如何利用模式匹配来执行函数，从而使我们能够清楚地区分不同的作业变量。

你可能已经注意到了，当我们把地图发送到 Faktory 时，我们使用了 atom 键。然而，当我们收到返回的数据时，我们得到了一个带有二进制键的映射。这是 Faktory 协议的副作用，因为它使用 JSON 在服务器和客户机之间传输数据。这是在构建 workers 时值得记住的一点，当匹配错误开始出现时，这将节省一些调试时间。

## 具有多个参数的作业

到目前为止，我们只向 Faktory 发送了一个值。让我们看看如何发送多条数据。

为此，我们需要向`perform_async/1`函数传递一个列表。当我们使用多个参数时，我们需要确保我们有一个接受相同数量参数的执行函数。

例如，如果我们要发送三条数据。

```
MyWorker.perform_async([1, 2, 3]) 
```

工人需要定义一个`perform/3`函数。让我们重构我们的`MathWorker`来接受多个参数而不是一个映射。

在`MathWorker`模块中，用新的`perform/3`版本替换`perform/1`功能。

```
def perform("+", x, y) do
  log(x + y)
end

def perform("-", x, y) do
  log(x - y)
end 
```

现在在 iex 中，我们可以使用一个列表来传递我们的计算。

```
iex(1)> ExampleApp.MathWorker.perform_async(["+", 3, 5])
The answer is: 8
:ok
iex(1)> ExampleApp.MathWorker.perform_async(["-", 10, 6])
The answer is: 4
:ok 
```

当我们考虑更复杂的用例时，传入多条数据的能力就显得很重要了。例如，假设我们正在构建一个工人来处理客户付款。我们可以将所有的信息合并到一张地图上，然后传给 Faktory，但是这感觉不对。使用多个参数允许我们在为支付工作分组数据时，保持应用程序已经期望的数据的相同形状。

例如，我们可以发送如下所示的作业。

```
iex(1)> PaymentWorker.perform_async([customer, payment_info, shipping_items]) 
```

然后在我们的 worker perform 函数中干净地处理它。

```
def perform(customer, payment_info, shipping_items) do
  # do work
end 
```

## 出了问题会怎么样？

我们在员工身上做的工作最终会出错。在软件中，这是我们无法逃避的现实，让我们看看工人如何帮助我们解决这个问题。

Faktory Worker 被设计为我们的故障保险，并期望工作人员执行的任何工作都可以在任何时候崩溃。这使得我们的工人非常严格，并期望取得成功。Faktory Worker 将捕捉异常，并将这些异常报告给 Faktory。如果作业失败，Faktory 将尝试使用指数补偿重试作业，直到成功或达到最大重试次数。正因为如此，我们必须确保我们的工人是等幂的。

让我们举一个有时会失败的工作的例子。像这样创建一个新的`FailureWorker`模块。

```
defmodule ExampleApp.FailureWorker do
  use FaktoryWorker.Job

  def perform(number) do
    random_number = Enum.random(1..3)

    if number == random_number do
      IO.puts("Yay, we found our number!")
    else
      raise "Nope, the number was #{random_number}"
    end
  end
end 
```

这个 worker 将生成一个随机数(1、2 或 3 ),如果它与我们作为作业参数提供的数字不匹配，就会引发异常。这大致模拟了我们有时在生产中看到的一些奇怪现象，例如网络连接中断或数据库查询超时。

让我们跳回 iex，选择一个数字。

```
iex(1)> ExampleApp.FailureWorker.perform_async(2)
:ok
20:36:12.551 [error] Task #PID<0.293.0> started from :worker_d117a98a82c04a50_8 terminating
** (RuntimeError) Nope, the number was 3
    (example_app) lib/example_app/failure_worker.ex:10: ExampleApp.FailureWorker.perform/1
    (elixir) lib/task/supervised.ex:90: Task.Supervised.invoke_mfa/2
    (elixir) lib/task/supervised.ex:35: Task.Supervised.reply/5
    (stdlib) proc_lib.erl:249: :proc_lib.init_p_do_apply/3
Function: &ExampleApp.FailureWorker.perform/1
    Args: [2] 
```

看来我们选错了号码。

查看错误输出，我们可以看到在一个由工人启动的进程中出现了一个`RuntimeError`。这就是 Faktory 工作人员如何处理我们的错误。我们所有的工作都在一个由工人监控的过程中运行。如果进程意外退出，worker 会捕获它并向 Faktory 报告错误。

如果我们打开 Faktory 控制面板，我们会在页面顶部看到一个重试报告。单击它将列出所有未决重试，并允许我们单击失败的作业并查看详细信息。在这里，我们可以看到我们的错误消息与堆栈跟踪一起列在错误部分下。如果没有列出任何重试，则作业可能已经重试并成功完成。

值得注意的是，Faktory 工作人员只关心在执行工作时发生的错误。如果作业成功完成(即没有因异常而崩溃)，它将被视为成功的作业。因此，从执行函数返回某种形式的错误元组不会导致作业失败。在这种情况下，通常对 success 元组进行严格匹配就足够了，如果返回错误，允许匹配失败。如果你因为任何原因需要明确地使一个任务失败，你可以像我们在上面的例子中做的那样提出一个错误，或者调用`Process.exit/2`给出一个退出原因。

## 总结

这篇文章介绍了使用 Faktory 的基础知识，如何执行工作和处理错误。这个库还有很多其他方面我们无法在这里介绍，我建议查看一下[文档](https://hexdocs.pm/faktory_worker/1.0.0/faktory-worker.html)以了解更多细节。

如果您在使用该库时有任何问题，请随时在 [Github repo](https://github.com/SeatedInc/faktory_worker) 上留下问题，或者您可以在 Elixir 论坛和 slack 频道上找到我。