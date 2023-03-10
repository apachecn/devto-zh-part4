# 在《科特林》中使用演员——一幕同时上演的戏剧

> 原文：<https://dev.to/domnikl/using-actors-in-kotlin-a-concurrent-play-in-one-act-3hn5>

今年 2 月，我已经写了关于 [Kotlin 协程及其在 JavaFX 应用程序中的使用](https://dev.to/domnikl/kotlin-coroutines-and-javafx-threads-26od/)的文章，今天我想进一步探索协程，并在 actors 的上下文中讨论它们。

## 为什么不是线程？

现代软件系统必须同时管理许多不同的任务。当有后台工作要做时，需要处理通过 web 界面、GUI 和其他渠道进入的用户请求。传统上，这是应用程序产生并提供工作的操作系统线程的领域。

[![Photo by Héctor J. Rivas on Unsplash](img/081150017d7fa49dd2a65d4fad1d90a1.png "Photo by Héctor J. Rivas on Unsplash")](https://res.cloudinary.com/practicaldev/image/fetch/s--Z2pyfJjq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9xbs2na48kvzncjsh9cd.jpg) 赫克托·j·里瓦斯摄影

但是你可能知道，线程很容易出错，而且在复杂的应用程序中遇到状态变化时不容易做好(如果你不知道，你应该阅读[为什么线程是一个坏主意(对于大多数目的)](https://web.stanford.edu/~ouster/cgi-bin/papers/threads.pdf))。有了`coroutines`，Kotlin 中有了一个轻量级的、简单的线程替代方案，它遵循了[通信顺序进程](https://de.wikipedia.org/wiki/Communicating_Sequential_Processes)的方法。这和激发 Go 的 goroutines 的想法是一样的:

> 不通过共享内存进行交流；相反，通过交流来分享记忆。- [有效去](https://golang.org/doc/effective_go.html#sharing)

科特林通过提供一个`Channel`的概念实现了这一点，它基本上是一个使用挂起函数的[队列。使用协程和通道，我们可以构建一个系统，以一种不需要任何锁和同步的方式封装可变状态，而是利用消息协议来处理该状态的并发更新。](https://kotlinlang.org/docs/reference/coroutines/channels.html#channel-basics)

## 演员模特

[![Photo by Avel Chuklanov on Unsplash](img/57bbd913ecf5e29d3d17a87ce73de59f.png "Photo by Avel Chuklanov on Unsplash")](https://res.cloudinary.com/practicaldev/image/fetch/s--i-1Qf2K0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ikohisoqozn1bfpcrwtg.jpg) 阿维尔·楚克兰诺夫摄

这样的模特叫演员。这不是一个新概念，相反，它已经存在了很多年，是 Erlang 的基础概念，也可以在 Java 和 Scala 中使用 Akka。在[中，仙丹中的 keeing 状态是如何工作的？](https://domnikl.github.io/2018/03/how-does-keeping-state-in-elixir-work/)我在《长生不老药》里造了一个演员，尽管这个概念在光束世界里并没有被正式称为‘演员’，而是被称为`gen_server`。

参与者有多种规模，他们可以执行多种任务，但他们都有相同的属性:

*   他们保持内部状态
*   他们同时运行
*   它们的状态只能通过消息来操纵
*   他们通过一个叫做“邮箱”的渠道接收信息
*   它们按顺序处理消息

## 不要在家里尝试这个！

好吧，这可能有点夸张，但有必要警告你。准确地说，是为了警告您`actor()`函数的实现状态。截至发稿时，创建一个 actor 是一个非常简单的 API，但这将在[kotlinx . coroutines](https://github.com/Kotlin/kotlinx.coroutines/issues/87)的未来版本中有所改变。

## 基本计数器示例

那么《科特林》里的演员长什么样？让我们看一看。

首先，我们需要定义可以发送给参与者的消息。在这个简单的例子中，actor 存储了一个计数器，该计数器可以使用`Message.Increment`增加任意整数值，当前值可以由`Message.GetValue`请求。

为了将数据发送回请求代码，我们在这里使用了一个`CompletableDeferred`。

```
sealed class Message {
    class Increment(val value: Int) : Message()
    class GetValue(val deferred: CompletableDeferred<Int>) : Message()
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们定义名为`basicActor`的演员本身。它需要被定义为`CoroutineScope`的扩展函数，因为它将启动一个协程来运行。它运行一个简单的`for`循环从通道中获取消息，这个循环将一直运行，直到有人关闭通道。消息的处理相当直接。

```
fun CoroutineScope.basicActor() = actor<Message> {
    var counter = 0

    for (message in channel) {
        when(message) {
            is Message.Increment -> counter += message.value
            is Message.GetValue -> message.deferred.complete(counter)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！但是要使用它，您还必须定义一个向参与者发送消息的客户端。下面是代码:

```
fun main() = runBlocking<Unit> {
    val channel = basicActor()

    channel.send(Message.Increment(1))
    channel.send(Message.Increment(2))

    val deferred = CompletableDeferred<Int>()

    channel.send(Message.GetValue(deferred))

    println(deferred.await()) // prints "3"

    channel.close()
} 
```

Enter fullscreen mode Exit fullscreen mode

在`main`中，我们通过使用`runBlocking()`定义了一个`CoroutineScope`,然后用它来启动演员并发送消息。`channel.close()`然后将导致演员完成。没有它，参与者将保持程序无限运行，这可能是典型的服务器情况下的理想状态。

## 结论

Actors 是一个简单的概念，可以帮助消除并发计算的麻烦。它们仍处于实验阶段，但这是朝着这个方向迈出的第一步，我对其发展很好奇，作为 Erlang 和 Elixir 的长期粉丝，我欣赏演员，并欢迎他们成为 Kotlin 协同程序的稳定组件。