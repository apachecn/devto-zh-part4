# 为什么我在 Kotlin 中不再使用协程

> 原文：<https://dev.to/martinhaeusler/why-i-stopped-using-coroutines-in-kotlin-kg0>

众所周知，我对 Kotlin 作为一种编程语言非常感兴趣，尽管它有一些缺点和奇怪的设计选择。我得到了一个使用 Kotlin、 [Kotlin 协程](https://kotlinlang.org/docs/reference/coroutines-overview.html)和协程驱动的服务器框架 [KTOR](https://ktor.io/) 的中型项目的工作机会。这些技术提供了很多优点，但是我发现与普通的老 Spring Boot 相比，它们很难使用。

> 免责声明:我不打算“抨击”任何这些东西，我的意图是提供我的“用户体验”,并解释为什么我将避免在未来使用它们。

# 调试

考虑下面这段代码:

```
suspend fun retrieveData(): SomeData {
    val request = createRequest()
    val response = remoteCall(request)
    return postProcess(response)
}

private suspend fun remoteCall(request: Request): Response {
   // do suspending REST call
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们假设我们想要调试`retrieveData`函数。我们在第一行放置一个断点。然后我们启动调试器(在我的例子中是 IntelliJ)，它在断点处停止。很好。现在我们执行一个`Step Over`(跳过呼叫`createRequest`)。那也行得通。然而，如果我们再次`Step Over`，程序将只是*运行*。它将在`remoteCall`之后**而不是**停止。

这是为什么呢？JVM 调试器被附加到一个`Thread`对象上。无论如何，这是一个非常合理的选择。然而，当协同程序加入进来时，一个线程不再做一件事情。仔细看:`remoteCall(request)`调用了一个`suspend` ing 函数——但是当我们调用它时，我们在语法中看不到它。那么会发生什么呢？我们告诉调试器“跳过”方法调用。调试器运行远程调用的代码并等待。

这就是事情变得困难的地方:当前线程(我们的调试器绑定到它)只是我们的协程的执行器。当我们调用一个`suspending`函数时会发生什么，在某个时刻，挂起的函数会`yield`。这意味着**一个不同的`Thread`将继续执行我们的方法**。我们有效地欺骗了调试器。

我发现的唯一解决方法是在我想去的行上放置一个断点，而不是使用`Step Over`。不用说，这是一个重大的痛苦。显然不仅仅是我。

此外，在一般调试中，很难确定单个协程当前正在做什么，因为它在线程之间跳转。当然，协程是有名称的，而且您可以让日志不仅打印线程，还打印协程名称，但是根据我的经验，调试基于协程的代码所需的脑力要比基于线程的代码高得多。

# 绑定数据到 REST 调用

在处理微服务时，一种常见的模式是接收带有某种形式的身份验证的 REST 调用，并将相同的身份验证传递给其他微服务的所有内部调用。在最简单的情况下，我们至少希望保留调用者的用户名。

然而，如果那些对其他微服务的调用嵌套在我们调用栈的 10 层深处呢？当然，我们不希望在每个函数中传递一个`authentication`对象作为参数。我们需要某种隐含的“语境”形式。

在 Spring 等传统的基于线程的框架中，这个问题的解决方案是使用一个`ThreadLocal`对象。这允许我们将任何类型的数据绑定到当前线程。只要一个线程对应于一个 REST 调用的处理(这是您应该一直追求的)，这正是我们所需要的。这种模式的一个很好的例子是春天的 [`SecurityContextHolder`](https://github.com/spring-projects/spring-security/blob/master/core/src/main/java/org/springframework/security/core/context/ThreadLocalSecurityContextHolderStrategy.java) 。

对于协程，情况有所不同。A `ThreadLocal`将不再起作用，因为您的工作负载将从一个线程跳到另一个线程；不再是一个线程在整个生命周期中伴随一个请求。在科特林协程中，有 [`CoroutineContext`](https://kotlinlang.org/docs/reference/coroutines/coroutine-context-and-dispatchers.html) 。本质上，它只不过是一个和协程一起运行的`HashMap`(不管它安装在哪个线程上)。它有一个可怕的过度设计的 API，使用起来很麻烦，但这不是这里的主要问题。

真正的问题是**协程不会自动继承上下文**。

例如:

```
suspend fun sum(): Int {
    val jobs = mutableListOf<Deferred<Int>>()
    for(child in children){
        jobs += async {  // we lose our context here!
            child.evaluate() 
        }
    }
    return jobs.awaitAll().sum()
} 
```

Enter fullscreen mode Exit fullscreen mode

每当你调用一个协程构建器，比如`async`、`runBlocking`或`launch`，默认情况下，你会丢失当前的协程上下文。你可以通过将上下文显式地传递给构建器方法来避免它，但是*上帝禁止*你忘记这么做(编译器不会在意的！).

子协程可以从一个空的上下文开始，如果对上下文元素的请求到来，但什么也没有找到，则可以向父协程上下文请求该元素。然而，这在 Kotlin 中不会发生，程序员每次都需要手动完成。

如果你对这个问题的细节感兴趣，我推荐你看看这篇博文。

# `synchronized`不再做自己认为该做的事

当在 Java 中使用锁或`synchronized`块时，我考虑的语义通常是“当我在这个块中时，其他人不能进入”。*没有别人*部分当然意味着存在某种形式的“身份”，在本例中就是`Thread`。这应该会在你的脑海中引起一个红色的警告信号。

让我们考虑下面的例子:

```
val lock = ReentrantLock()

suspend fun doWithLock(){
   lock.withLock {
       callSuspendingFunction()
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是*危险的*。即使`callSuspendingFunction()`没有做任何有害的事情，代码也不会像你想象的那样运行:

*   进入锁
*   调用暂停函数
*   协程让步，当前线程仍然持有锁
*   另一个线程获得了我们的协程
*   我们是同一个协程，但我们不再拥有锁！

潜在冲突、死锁或其他不安全情况的数量惊人。你可能会说我们“仅仅”需要设计我们的代码来处理这些。我同意，但是我们正在谈论 JVM。外面有一个庞大的图书馆生态系统。他们不准备处理这件事。

这里的结果是:**从您开始使用协程的那一刻起，您就丧失了使用大量 Java 库的可能性，仅仅因为它们期望一个基于线程的环境。**

# 吞吐量与水平扩展

对于服务器端来说，协程的一个很大的优点是单线程可以处理更多的请求；当一个请求等待数据库响应时，同一个线程可以愉快地为另一个请求服务。特别是对于 I/O 受限的任务，这可以增加吞吐量。

然而，正如这篇博文所希望向您展示的，在许多层面上，*是*与使用协程相关的非零成本。

由此产生的问题是:这种收益值得付出这样的代价吗？在我看来答案是**不**。在云和微服务环境中，应该总是有一些扩展机制，无论是 Google AppEngine、AWS Beanstalk 还是某种形式的 Kubernetes。如果当前负载增加，这些技术将简单地按需产生新的微服务实例。因此，考虑到使用协程时我们必须跨越的所有障碍，单个实例能够处理的吞吐量就不那么重要了。这降低了我们从使用协程中获得的价值。

# 协程有它们的位置

协程仍然有用。当开发只有一个 UI 线程的客户端 UI 时，协同例程可以帮助改进代码结构，同时符合 UI 框架的要求。我听说这在 Android 上运行得很好。协程是一个有趣的话题，但是对于服务器端，我觉得我们还没有到那一步。JVM 开发人员目前正在开发[纤程](https://www.youtube.com/watch?v=vbGbXUjlRyQ)，本质上也是协程，但是他们的目标是很好地与 JVM 基础设施配合。看看这项工作如何发展，以及 Jetbrains 将如何对他们自己的协程解决方案做出反应，将会很有趣。在最好的情况下，Kotlin 协程将来会“映射”到纤程，调试器会足够聪明地正确处理它们。