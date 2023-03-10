# Mix 和 OTP 10 :分布式处理任务和设置

> 原文：<https://dev.to/gumi/mix-otp-10-3i31>

本文在 Elixir 官网的许可下，根据“[distributed tasks and configuration](https://elixir-lang.org/getting-started/mix-otp/distributed-tasks-and-configuration.html)”的解说，加以修改，对任务的分散处理和 APP 设定进行说明。

这次，在`kv`APP 中添加路由的层。 允许根据进程名称在节点之间传递请求。 路由层按以下格式接收路由表:

```
[
  {?a..?m, :"foo@computer-name"},
  {?n..?z, :"bar@computer-name"}
] 
```

Enter fullscreen mode Exit fullscreen mode

路由器在表中检查进程名称的第一个字节，并据此将其分发到相应的节点。 例如，以字母" a "开头的进程被发送到节点`foo@computer-name`。 另外，`?a`是字符" a "的 Unicode 代码点(参照“[Elixir 入门 06 :二进制文件和字符串以及字符列表](https://dev.to/gumi/elixir-06--35na)”“UTF-8 和 Unicode”)。

如果匹配的条目遇到正在评估请求的节点，则该节点将执行请求的操作，而不进行路由。 如果匹配的条目指示另一个节点，则将请求传递给该节点。 节点查看其路由表(将与第一个节点不同)，并相应地采取行动。 如果没有匹配的条目，则出错。

它不会让在路由表中找到的节点立即执行请求的操作。 将路由请求传递给节点进行处理。 如果是上述简单的路由表，也可以由所有节点共享吧。 但是，按照传递路由请求的方式，当 APP 扩大时，路由表可以很容易地分成较小的部分。 长大到一定程度后，`foo@computer-name`只负责将请求路由到进程，处理请求的进程将被发送到其他节点。 这样的话，`bar@computer-name`就不知道变更了。

> 本文在一台机器上使用两个节点。 在同一网络中，也可以使用多台机器。 那种情况下需要准备。 首先，所有计算机上都有文件`~/.erlang.cookie`，其值必须完全相同。 第 2，确认[`epmd`](http://www.erlang.org/doc/man/epmd.html)在未屏蔽的端口上运行(通过`epmd -d`可以获得调试信息)。 更详细地说，“Learn You Some Erlang for Great Good！ ”的“[Distribunomicon](https://learnyousomeerlang.com/distribunomicon) ”一章。

# 编写分散处理的代码

Elixir 提供了连接节点并相互交换信息的功能。 实际上，在分布式环境中发送和接收流程和消息使用的是相同的思路。 因为 Elixir 的过程无论在哪里。 这意味着发送消息时，接收方的进程不考虑是否是同一节点。 无论是哪一种，虚拟机都会传递消息。

要执行分布式处理的代码，必须命名并启动虚拟机。 名称可以很短(对于同一网络)，也可以很长(完整的计算机地址)。 请按如下方式开始 IEx。

```
$  iex --sname foo 
```

Enter fullscreen mode Exit fullscreen mode

提示显示会稍有变化。 节点名称后带`@`的是计算机名称。

```
Erlang/OTP 21 [erts-10.0.5] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1] [hipe] [dtrace]

Interactive Elixir (1.7.3) - press Ctrl+C to exit (type h() ENTER for help) iex(foo@computer-name)1> 
```

Enter fullscreen mode Exit fullscreen mode

按如下方式在壳中定义模块:

```
iex> defmodule Hello do
...>   def world, do: IO.puts "hello world"
...> end
{:module, Hello,
 <<70, 79, 82, 49, 0, 0, 4, 60, 66, 69, 65, 77, 65, 116, 85, 56, 0, 0, 0, 140,
   0, 0, 0, 15, 12, 69, 108, 105, 120, 105, 114, 46, 72, 101, 108, 108, 111, 8,
   95, 95, 105, 110, 102, 111, 95, 95, 7, ...>>, {:world, 0}} 
```

Enter fullscreen mode Exit fullscreen mode

如果在同一网络上的其他计算机上安装了 Erlang 和 Elixir，则可以启动其他 shell。 否则，从其他终端打开 IEx 的会话就可以了吧。 无论哪一种，都要取一个简短的别的名字，如下所示。

```
$  iex --sname bar 
```

Enter fullscreen mode Exit fullscreen mode

不能从新的其他会话中调用刚才指定的模块的函数。

```
iex(bar@computer-name)> Hello.world
** (UndefinedFunctionError) function Hello.world/0 is undefined (module Hello is not available)
    Hello.world() 
```

Enter fullscreen mode Exit fullscreen mode

但是，使用[`Node.spawn_link/2`](https://hexdocs.pm/elixir/Node.html#spawn_link/2)，从`bar@computer-name`到`foo@computer-name`将建立新的进程(节点名称中的 computer-name 请替换为提示中的名称)。

```
iex(bar@computer-name)> Node.spawn_link :"foo@computer-name", fn -> Hello.world end             
hello world
#PID<10577.117.0> 
```

Enter fullscreen mode Exit fullscreen mode

Elixir 在另一个节点上创建进程，并返回了其 PID。 然后，代码在指定了函数的另一个节点上执行，并调用了函数。 请注意，当前节点显示的是函数的输出，而不是其他节点。 也就是说，消息从其他节点发回，并输出到当前节点。 这是因为在其他节点上创建的进程具有与当前节点相同的组组长(参照“[Elixir 入门 12 :输入输出和文件系统](https://qiita.com/fumio_nonaka/private/1861765a3a108a74ceff)”“进程和组组长”)。

使用`Node.spawn_link/2`返回的 PID，可以发送信息进一步接收。

```
iex(bar@computer-name)> pid = Node.spawn_link :"foo@computer-name", fn ->
...(bar@computer-name)>   receive do
...(bar@computer-name)>     {:ping, client} -> send client, :pong
...(bar@computer-name)>   end
...(bar@computer-name)> end
#PID<10577.119.0>
iex(bar@computer-name)> send pid, {:ping, self()}
{:ping, #PID<0.106.0>}
iex(bar@computer-name)> flush()
:pong
:ok 
```

Enter fullscreen mode Exit fullscreen mode

明白了为了进行分散处理，每次处理都在`Node.spawn_link/2`中在远程节点生成进程即可。 但是，应该尽量避免在监视树之外制作流程。 在这次的安装中，使用`Node.spawn_link/2`更理想的方法可以考虑 3 个。

1.  使用 Erlang 的[`:rpc`](http://erlang.org/doc/man/rpc.html)模块可以执行远程节点的函数。 例如，如果从外壳取`:rpc.call(:"foo@computer-name", Hello, :world, [])`，则可以调用其他节点的函数`Hello.world/0`。
2.  [根据`GenServer`](https://hexdocs.pm/elixir/GenServer.html)的 API，只要在其他节点启动服务器，就可以发送请求。 例如，可以通过`GenServer.call({name, node}, arg)`调用远程节点的服务器。 第一参数可以是远程进程的 PID。
3.  也可以使用[`Task`](https://hexdocs.pm/elixir/Task.html)在本地和远程节点上生成(请参见“[Mix 和 OTP 08 :任务和 gen_tcp](https://qiita.com/drafts/f94e179f3490b1a8f618) )。

使用`:rpc`和`GenServer`时，请求序列化到一个服务器上。 另一方面，`Task`在远程节点上被有效地异步处理。 序列化的唯一点是由管理员生成。 在这次的路由层中，决定使用`Task`。 但是，其他方法也没有问题。

# 异步/等待

到目前为止，开始的`Task`是单独执行的。 另外，返回值也没有确认。 但是，在任务中处理值并稍后查看结果可能会有帮助。 因此，`Task`具备的是`async/await`巴丹。

`async/await`是同时处理值的简单机制。 不仅如此，还可以在同一个`Task.Supervisor`中使用`async/await`(参照“[Mix 和 OTP 08 :任务和 gen_tcp](https://qiita.com/drafts/f94e179f3490b1a8f618) ”“任务管理员”)。 只需调用[`Task.Supervisor.async/3`](https://hexdocs.pm/elixir/Task.Supervisor.html#async/3)代替`Task.Supervisor.start_child/3`，然后用[`Task.await/2`](https://hexdocs.pm/elixir/Task.html#await/2)读取结果即可。

```
task = Task.async(fn -> compute_something_expensive end)
res  = compute_something_else()
res + Task.await(task) 
```

Enter fullscreen mode Exit fullscreen mode

# 任务的分散处理

任务的分布式处理与任务的监视基本上没有区别。 不同之处在于，在给管理员创建任务时，要传递节点名称。 `:kv`打开 APP 应用程序的`lib/kv/supervisor.ex`，在`init/1`的`children`列表的末尾加上`Task.Supervisor`，如下所示。

```
def init(:ok) do
  children = [
    {DynamicSupervisor, name: KV.BucketSupervisor, strategy: :one_for_one},
    {KV.Registry, name: KV.Registry},
    {Task.Supervisor, name: KV.RouterTasks}  # 追加
  ]
  Supervisor.init(children, strategy: :one_for_all)
end 
```

Enter fullscreen mode Exit fullscreen mode

重新启动命名节点。 但是，`:kv`请从 APP 演示的目录中打开。

```
$  cd apps/kv
$  iex --sname foo -S mix 
```

Enter fullscreen mode Exit fullscreen mode

对于另一个节点也是如此。

```
$  iex --sname bar -S mix 
```

Enter fullscreen mode Exit fullscreen mode

这样，管理员就可以从一个节点直接在另一个节点上完成任务。 以下分布式处理任务获取运行任务的节点的名称。

```
iex(bar@computer-name)> task = Task.Supervisor.async {KV.RouterTasks, :"foo@computer-name"}, fn ->
...(bar@computer-name)>   {:ok, node()}
...(bar@computer-name)> end
%Task{
  owner: #PID<0.140.0>,
  pid: #PID<15195.164.0>,
  ref: #Reference<0.1065362951.4240965635.67142>
}
iex(bar@computer-name)> Task.await(task)
{:ok, :"foo@computer-name"} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我给了`Task.Supervisor.async/3`一个无名函数。 但是，在分布式处理中，最好明确提供模块、函数和参数。 在无名函数中，目标节点必须具有与调用方完全相同的代码版本。 [如果使用`Task.Supervisor.async/4`](https://hexdocs.pm/elixir/Task.Supervisor.html#async/4)，在传递给自变量的模块中只要有与真实性一致的函数就可以了，所以可以说更加牢固。

```
iex(bar@computer-name)> task = Task.Supervisor.async {KV.RouterTasks, :"foo@computer-name"}, Kernel, :node, []   
%Task{
  owner: #PID<0.140.0>,
  pid: #PID<15195.165.0>,
  ref: #Reference<0.1065362951.4240965635.67197>
}
iex(bar@computer-name)> Task.await(task)
:"foo@computer-name" 
```

Enter fullscreen mode Exit fullscreen mode

# 路由层

在文件`lib/kv/router.ex`中如下规定路由器的模块`KV.Router`。 此外，请将 computer-name 改写为本地计算机名称。

```
defmodule KV.Router do
  @doc """
  与えられた`mod`の`fun`に`args`が渡された要求を
  プロセス`bucket`にもとづいて適切なノードに送る。
  """
  def route(bucket, mod, fun, args) do
    # バイナリの最初のバイトを得る
    first = :binary.first(bucket)

    # table()からエントリーを探してなければエラー
    entry =
      Enum.find(table(), fn {enum, _node} ->
        first in enum
      end) || no_entry_error(bucket)

    # エントリーが現ノードの場合
    if elem(entry, 1) == node() do
      apply(mod, fun, args)
    else
      {KV.RouterTasks, elem(entry, 1)}
      |> Task.Supervisor.async(KV.Router, :route, [bucket, mod, fun, args])
      |> Task.await()
    end
  end

  defp no_entry_error(bucket) do
    raise "could not find entry for #{inspect bucket} in table #{inspect table()}"
  end

  @doc """
  ルーティングテーブル
  """
  def table do
    # computer-nameはローカルマシン名に置き替える
    [{?a..?m, :"foo@computer-name"}, {?n..?z, :"bar@computer-name"}]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

通过测试确认路由器的动作吧。 在`test/kv/router_test.exs`中写以下两个测试。

```
defmodule KV.RouterTest do
  use ExUnit.Case, async: true

  test "route requests across nodes" do
    assert KV.Router.route("hello", Kernel, :node, []) ==
           :"foo@computer-name"
    assert KV.Router.route("world", Kernel, :node, []) ==
           :"bar@computer-name"
  end

  test "raises on unknown entries" do
    assert_raise RuntimeError, ~r/could not find entry/, fn ->
      KV.Router.route(<<0>>, Kernel, :node, [])
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在最初的测试中，通过调用[`Kernel.node/0`](https://hexdocs.pm/elixir/Kernel.html#node/0)获得执行节点名。 因为传递“hello”和“world”作为进程名，所以路由表将分别返回`foo@computer-name`和`bar@computer-name`。

第二个测试确定对于不知道的条目是否会发生错误。

要运行第一个测试，必须运行两个节点。 请移至目录`apps/kv`，如下启动用于测试的另一个节点。

```
$  iex --sname bar -S mix 
```

Enter fullscreen mode Exit fullscreen mode

然后，从原始节点执行以下测试，应该正确无误。

```
$  elixir --sname foo -S mix test 
```

Enter fullscreen mode Exit fullscreen mode

# 测试的过滤器和标签

前项的测试如下。 但是，测试的构成会更复杂吧。 实际上，现在用`mix test`尝试的话，也会失败。 因为它包含了提示连接到其他节点的测试。

```
1) test route requests across nodes (KV.RouterTest)
    test/kv/router_test.exs:4
 ** (exit) exited in: GenServer.call({KV.RouterTasks, :"foo@computer-name"}, {:start_task, [#PID<0.169.0>, :monitor, {:nonode@nohost, #PID<0.169.0>}, {KV.Router,:route, ["hello", Kernel, :node, []]}], :temporary, nil}, :infinity)
 ** (EXIT) no connection to foo@computer-name
    code: assert KV.Router.route("hello", Kernel, :node, []) ==
    stacktrace:
      (elixir) lib/gen_server.ex:924: GenServer.call/3
      (elixir) lib/task/supervisor.ex:377: Task.Supervisor.async/6
      (kv) lib/kv/router.ex:21: KV.Router.route/4
      test/kv/router_test.exs:5: (test) 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，ExUnit 具有标记测试的功能。 您可以只执行特定的回调，也可以根据标签进行过滤。 另外，也有像`:capture_log`那样预先设定在 ExUnit 中的标签(参照“[Mix 和 OTP 09: DocTest 和 with 的模式匹配](https://qiita.com/drafts/553e9445dd1e38169496)”“执行指令”)。

因此，让我们对`test/kv/router_test.exs`做如下标记吧。 `@tag :distributed`与写`@tag distributed: true`相同。

```
@tag :distributed  # 追加
test "route requests across nodes" do
  assert KV.Router.route("hello", Kernel, :node, []) ==
          :"foo@computer-name"
  assert KV.Router.route("world", Kernel, :node, []) ==
          :"bar@computer-name"
end 
```

Enter fullscreen mode Exit fullscreen mode

如果正确标记了测试，在测试时用[`Node.alive?/0`](https://hexdocs.pm/elixir/Node.html#alive?/0)确认网络上是否有节点就可以了吧。 如果没有的话，就省略分散处理的测试。 因此，请在`:kv`APP (`apps/kv`)的`test/test_helper.exs`中填写如下。

```
exclude =
  if Node.alive?, do: [], else: [distributed: true]  # 追加

# ExUnit.start()
ExUnit.start(exclude: exclude) 
```

Enter fullscreen mode Exit fullscreen mode

这样，`mix test`就能毫无失败地奔跑了。 因为 ExUnit 省略了分布式处理的一个测试。

```
$  mix test
Excluding tags: [distributed: true] ......  Finished in 2.0 seconds
7 tests, 0 failures, 1 excluded 
```

Enter fullscreen mode Exit fullscreen mode

节点`bar@computer-name`可用时，如下测试包括分散处理在内吧。

```
$  elixir --sname foo -S mix test
.......  Finished in 2.0 seconds
7 tests, 0 failures 
```

Enter fullscreen mode Exit fullscreen mode

`mix test`命令可以动态地包含和删除标签。 例如，如果输入`mix test --include distributed`，则无论`test/test_helper.exs`的设定如何，都将包含分散处理的测试。 或者，在命令行中传递`--exclude`可以删除特定的标签。 另外，也可以使用`--only`只执行特定标签的测试，如下所示。

```
$  elixir --sname foo -S mix test --only distributed
Including tags: [:distributed]
Excluding tags: [:test] .  Finished in 0.06 seconds
7 tests, 0 failures, 6 excluded 
```

Enter fullscreen mode Exit fullscreen mode

关于过滤器，请阅读`ExUnit.Case`模块的“[Filters](https://hexdocs.pm/ex_unit/ExUnit.Case.html#module-filters) ”。

# APP 环境和设定

路由表直接打了`KV.Router`如下。 让这张桌子动态起来吧。 这样，不仅可以设置开发和测试或产品，还可以将在不同条目中运行的不同节点添加到路由表中。 OTP 正是有那个功能的。 那就是 APP 应用环境。

```
def table do
  # computer-nameはローカルマシン名に置き替える
  [{?a..?m, :"foo@computer-name"}, {?n..?z, :"bar@computer-name"}]
end 
```

Enter fullscreen mode Exit fullscreen mode

每个 APP 切换都有一个环境，密钥具有唯一的设置。 例如，路由表可以包含在`:kv`APP 环境中。 这是缺省值，其他 APP 应用程序可以根据需要改变表。

打开`apps/kv/mix.exs`，按如下方式填写函数`application/0`的返回值。 赋予 APP 的是键`:env`。 这将成为 APP 应用的默认环境。 条目为键`:routing_table`，值为空列表。 现在，APP 应用程序环境在出厂时为空表。 使用的表取决于测试和开发的配置。

```
def application do
  [
    extra_applications: [:logger],
    env: [routing_table: []],  # 追加
    mod: {KV, []}
  ]
end 
```

Enter fullscreen mode Exit fullscreen mode

要在代码中使用 APP 环境，请将`KV.Router.table/0`的规定改写如下。 [`Application.fetch_env!/2`](https://hexdocs.pm/elixir/Application.html#fetch_env!/2)读取`:routing_table`为`:kv`的环境中的条目。 有关 APP 环境的操作，请参阅“[`Application behaviour`](https://hexdocs.pm/elixir/Application.html)”。

```
def table do
  # [{?a..?m, :"foo@computer-name"}, {?n..?z, :"bar@computer-name"}]
  Application.fetch_env!(:kv, :routing_table)
end 
```

Enter fullscreen mode Exit fullscreen mode

由于路由表已清空，分布式处理测试应该会失败。 重新执行测试确认一下吧。

```
$  iex --sname bar -S mix 
```

Enter fullscreen mode Exit fullscreen mode

```
$  elixir --sname foo -S mix test --only distributed 
```

Enter fullscreen mode Exit fullscreen mode

```
1) test route requests across nodes (KV.RouterTest)
    test/kv/router_test.exs:5
    ** (RuntimeError) could not find entry for "hello" in table []
    code: assert KV.Router.route("hello", Kernel, :node, []) ==
    stacktrace:
      (kv) lib/kv/router.ex:27: KV.Router.no_entry_error/1
      (kv) lib/kv/router.ex:14: KV.Router.route/4
      test/kv/router_test.exs:6: (test) 
```

Enter fullscreen mode Exit fullscreen mode

APP 应用环境的好处在于，它不仅可以设置在当前的 APP 应用程序中，还可以设置在整个 APP 应用程序中。 整体设定用`config/config.exs`进行。 例如，打开`apps/kv/config/config.exs`，在最后添加以下代码。 这是 IEx 默认提示符的规定。

```
config :iex, default_prompt: ">>>" 
```

Enter fullscreen mode Exit fullscreen mode

```
$  iex -S mix
Erlang/OTP 21 [erts-10.0.5] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1] [hipe] [dtrace]

Compiling 5 files (.ex)
Generated kv app
Interactive Elixir (1.7.3) - press Ctrl+C to exit (type h() ENTER for help) >>> 
```

Enter fullscreen mode Exit fullscreen mode

同样，可以在`apps/kv/config/config.exs`文件中规定`:routing_table`如下。

```
# computer-nameはローカルマシン名に置き替える
config :kv, :routing_table, [{?a..?m, :"foo@computer-name"}, {?n..?z, :"bar@computer-name"}] 
```

Enter fullscreen mode Exit fullscreen mode

如果重新启动节点并测试分布式处理，这次应该是。

`:kv`APP 是安布雷拉项目中的一个。 从 Elixir v1.2 开始，Delabrer APP 应用程序共享`config/config.exs`的设置。 因为根据项目根目录的`config/config.exs`的以下记述，将读取所有子项的设定。

```
import_config "../apps/*/config/config.exs" 
```

Enter fullscreen mode Exit fullscreen mode

`mix run`命令加上`--config`标志后，会根据需要给出设定文件。 可以启动不同的节点，每个节点都有不同的设置(例如，改变路由表)。

由于内置的用于 APP 表示设置的功能，在部署模糊 APP 表示时可以考虑多种方法。

*   在节点上部署解绑定器 APP，以用于 TCP 服务器和密钥/值数据存储
*   `:kv_server`部署 APP 部署仅用于 TCP 服务器，路由表中仅指定其他节点
*   `:kv`只部署 APP 应用程序，节点只用于数据保存(不进行 TCP 访问)

即使将来 APP 的数量增加，部署粒度也可以保持在相同的级别。 制作的时候，请考虑将哪个 APP 应用程序设定为哪个。

构建多个版本时，可能会考虑使用[Distillery](https://github.com/bitwalker/distillery) 这样的工具。 您可以选择 APP 应用程序和设置，并将其打包，包括当前的 Erlang 和 Elixir。 这样，即使目标系统上未安装运行时，也可以部署 APP 应用程序。

在产品中使用这种密钥/值数据保存的分散处理时，推荐使用[Riak](http://basho.com/products/riak-kv/) 。 Riak 也在 Erlang VM 上运行。 子进程将被复制以避免数据丢失。 用[一致散列法](https://ja.wikipedia.org/wiki/%E3%82%B3%E3%83%B3%E3%82%B7%E3%82%B9%E3%83%86%E3%83%B3%E3%83%88%E3%83%8F%E3%83%83%E3%82%B7%E3%83%A5%E6%B3%95)代替路由器，进程被映射到节点。 即使添加了存储进程的新节点，并且必须迁移数据，这种算法也有助于减少数据量。

#### Mix 和 OTP 也抽签

*   [尝试使用 Mix 和 OTP 01:mix](https://dev.to/gumi/mixotp-01-mix-dg1)
*   MixとOTP 02:经纪人
*   MixとOTP 03:基因服务器
*   [Mix 和 OTP 04 :管理员和 APP 沟通](https://dev.to/gumi/mix-otp-04-bj0)
*   [Mix 和 OTP 05 :动态管理员](https://dev.to/gumi/mix-otp-05-l3p)
*   [MixとOTP 06: ETS](https://dev.to/gumi/mix-otp-06-ets-ol3)
*   [Mix 和 OTP 07 :依存关系和模糊项目](https://dev.to/gumi/mix-otp-07-1p5k)
*   MixとOTP 08: Taskとgen_tcp
*   [Mix 和 OTP 09: DocTest 和 with 的模式匹配](https://dev.to/gumi/mix-otp-09-doctest-with-533g)
*   Mix 和 OTP 10 :分布式处理任务和设置