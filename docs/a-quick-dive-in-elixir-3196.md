# 快速潜入长生不老药

> 原文：<https://dev.to/kyleparisi/a-quick-dive-in-elixir-3196>

# 什么是仙丹？

又一种编程语言。elixir 有一些我在其他语言中没有体验过的独特特征。首先，我学习一门新语言的方法可能会很有趣。我所做的是为语言或框架启动一个回购，在这种情况下是`elixir-kiss`(保持简单愚蠢)。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[仙丹之吻](https://github.com/kyleparisi/elixir-kiss)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 仙丹之吻

## 使用

```
mix deps.get
# terminal 1
iex --name a@127.0.0.1 -S mix
# terminal 2
iex --name b@127.0.0.1 -S mix

# either terminal, Node.list.  example:
iex(b@127.0.0.1)1> Node.list
[:"a@127.0.0.1"]
```

</article>

[View on GitHub](https://github.com/kyleparisi/elixir-kiss)

从那里，我开始探索我感兴趣的基本想法，并在最初的测试基础上建立分支。

# 仙丹可以做什么？

你会发现长生不老药的主要用途是某种分布式系统。将一群计算机想象成一个单独的应用程序是一个有趣的想法。要将应用程序“联网”在一起，您可以用几行代码来完成。下面是一个使用集群 2 个节点的库的例子。

```
defmodule MyApp.App do
  use Application

  def start(_type, _args) do
    topologies = [
      example: [
        strategy: Cluster.Strategy.Epmd,
        config: [hosts: [:"a@127.0.0.1", :"b@127.0.0.1"]],
      ]
    ]
    children = [
      {Cluster.Supervisor, [topologies, [name: MyApp.ClusterSupervisor]]},
      # ..other children..
    ]
    Supervisor.start_link(children, strategy: :one_for_one, name: MyApp.Supervisor)
  end
end 
```

有几种方法可以对应用程序进行集群。上面的例子使用了一个名为 [Erlang 端口映射器守护进程](http://erlang.org/doc/man/epmd.html)的内置策略。可以使用 DNS、多播 UDP gossip 或其他基于 API 的方法。

# 仙丹有什么独特之处？

仙丹有许多独特的属性。Elixir 的控制流在很大程度上基于模式匹配。这意味着您根据传入的数据结构定义函数。那看起来有点像:

```
 post "/login" do
    {:ok, body, conn} = read_body(conn)
    case Poison.decode(body) do
      {:ok, %{"email" => "", "password" => ""}} ->
        send_resp(conn, 400, Poison.encode!(%{errors: %{email: "Please provide an email.", password: "Please provide a password"}}))
      {:ok, %{"email" => email, "password" => password}} ->
        send_resp(conn, 200, "")
      {:error, :invalid, 0} ->
        Logger.info("No body provided for /login")
        send_resp(conn, 400, Poison.encode!(%{errors: %{email: "Please provide an email.", password: "Please provide a password"}}))
      {:ok, %{"email" => email}} ->
        Logger.info("No password provided for /login")
        send_resp(conn, 400, Poison.encode!(%{errors: %{password: "Please provide a password"}, email: email}))
      {:ok, %{"password" => _}} ->
        Logger.info("No email provided for /login")
        send_resp(conn, 400, Poison.encode!(%{errors: %{email: "Please provide an email."}}))
    end
  end 
```

在 elixir 中没有 return 或 break 语句。函数的最后一行是 return 语句。这次真的让我措手不及。Return 语句绝对是为过程心态设计的。没有返回实际上强调了模式匹配功能。

文档是一等公民。我从没见过这个。

```
defmodule Math do
  @moduledoc """
  Provides math-related functions.

  ## Examples

      iex> Math.sum(1, 2)
      3

  """

  @doc """
  Calculates the sum of two numbers.
  """
  def sum(a, b), do: a + b
end 
```

如果您配置您的测试来使用 doctests，它将实际上断言`iex>`语句。非常酷。

运行酏剂可能相当复杂，因为它的分布式性质。但是 elixir 使用的是 erlang，它碰巧内置了一些方便的 GUI 工具。你可以在这里看到一些 T2。

> “让它崩溃吧”。elixir——和 Erlang——鼓励你编码幸福之路。应该允许运行时错误使进程崩溃。 <sup id="fnref1">[1](#fn1)</sup>

最后，elixir 有一个`Read Eval Print Loop` (REPL)运行时能力，可以很容易地测试思想和探索正在运行的应用程序。

# 我不喜欢什么

即使 elixir 有合理的类型(元组、映射、列表等。)，做类型转换我发现很混乱。如果我想把映射中的一些东西转换成用户定义的结构，这并不容易。

我对数据库驱动程序不太满意。最推荐的接口是一个名为 [Ecto](https://hexdocs.pm/ecto/Ecto.html) 的抽象库。要使用此接口，您必须定义仓库、模式、变更集或查询。这些查询看起来像某种类型的`Domain Specific Language` (DSL)，而 changsets 对我来说是一个陌生的概念。令人惊讶的是，连接、准备好的语句和查询不是建议的驱动因素。

一些库文档，尽管有大量的文档，有时看起来并不是很有帮助。

# 最后的想法

没有一种语言是解决所有问题的灵丹妙药。长生不老药也不例外。它仍然是一种年轻的语言，最初是在 2011 年创建的，它的基础是基于更古老的 erlang。当我需要分布或集群时，我可能会回头看看它。

一些有趣的链接:

*   [https://elixirschool.com/en/](https://elixirschool.com/en/)
*   [https://engineering . dollarshaveclub . com/elixir-OTP-applications-on-kubernetes-9944636 b 8609](https://engineering.dollarshaveclub.com/elixir-otp-applications-on-kubernetes-9944636b8609)
*   [http://tech . nextroll . com/blog/dev/2018/01/08/quaff-that-药水-用仙丹和 erlang 拯救百万人. html](http://tech.nextroll.com/blog/dev/2018/01/08/quaff-that-potion-saving-millions-with-elixir-and-erlang.html)
*   [https://tonyc . github . io/posts/managing-external-commands-in-elixir-with-ports/](https://tonyc.github.io/posts/managing-external-commands-in-elixir-with-ports/)
*   [https://blog . carbon five . com/2018/01/30/comparing-dynamic-supervision-strategies-in-elixir-1-5-and-1-6/](https://blog.carbonfive.com/2018/01/30/comparing-dynamic-supervision-strategies-in-elixir-1-5-and-1-6/)
*   [https://github.com/plausible-insights/plausible](https://github.com/plausible-insights/plausible)
*   [Youtube -热门代码交换](https://www.youtube.com/watch?v=CZWMc2cXUAw)

* * *

1.  [https://10 consulting . com/presentations/building-cqrs-es-we B- applications-in-elixir/# 34](https://10consulting.com/presentations/building-cqrs-es-web-applications-in-elixir/#34)↩