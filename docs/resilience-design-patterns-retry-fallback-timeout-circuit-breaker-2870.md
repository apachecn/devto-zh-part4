# 弹性设计模式:重试、回退、超时、断路器

> 原文：<https://dev.to/frosnerd/resilience-design-patterns-retry-fallback-timeout-circuit-breaker-2870>

# 什么是韧性？

软件本身不是目的:它支持您的业务流程并让客户满意。如果软件不在生产环境中运行，它就不能产生价值。然而，高效的软件也必须是正确的、可靠的和可用的。

当谈到软件设计中的弹性时，主要目标是构建健壮的组件，这些组件可以容忍其范围内的错误，也可以容忍它们所依赖的其他组件的故障。虽然自动故障转移或冗余等技术可以使组件容错，但现在几乎每个系统都是分布式的。即使是简单的 web 应用程序也可以包含 web 服务器、数据库、防火墙、代理、负载平衡器和缓存服务器。此外，网络基础设施本身由许多组件组成，总会在某个地方发生故障。

除了完全失败的情况之外，服务也可能需要更长的时间来响应。实际上，尽管他们的响应格式是正确的，但他们甚至可能以错误的方式进行语义回答。同样，一个系统的组件越多，出故障的可能性就越大。

可用性通常被认为是一个重要的质量属性。它表示一个组件实际可用的时间，与该组件应该可用的时间相比。它可以用下面的公式表示:

[![](img/502d629de27afc22b8f330bbf5d7a0bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s0kBf03g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m9297nxz6snzkhevm09u.png)

传统方法旨在增加正常运行时间，而现代方法旨在减少恢复时间，从而减少停机时间。这是有用的，因为它让我们能够处理失败，而不是不惜一切代价试图阻止它们，并在它们发生的情况下长时间不可用。Uwe Friedrichsen 将[弹性设计模式](https://www.slideshare.net/ufried/patterns-of-resilience)分为四类:*松耦合*、*隔离*、*延迟控制*和*监督*。

[![](img/ce1891d292d6fdffd3c6a881b827baa5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0IP86XgP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qstzssj2v491i412pkss.png)

在这篇博文中，我们想看看延迟控制类别中的四种模式:*重试*、*回退*、*超时*和*断路器*。在理论介绍之后，我们将看到如何使用 Eclipse Vert.x 在实践中应用这些模式。我们将通过讨论替代实现和总结发现来结束这篇文章。

# 图案

## 示例场景

为了说明模式的功能，我们将利用一个非常简单的用例。想象支付服务是购物平台的一部分。当客户想要付款时，支付服务应该确保没有欺诈意图。为此，它要求欺诈检查服务。

在这种情况下，我们的服务提供基于 HTTP 的接口。以检查交易，支付服务向欺诈检查服务发送 HTTP 请求。如果一切正常，将会有一个 200 的响应，布尔值表示交易是否是欺诈性的。但是如果欺诈检查服务没有回答呢？如果它返回一个内部服务器错误(500)怎么办？

[![](img/bbfb9d5ef711d844edf0d793f49b4393.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cjh6826A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/43ty7806f1p9i1k1o79s.png)

现在让我们来看看解决可能的沟通问题的四种具体模式。虽然这是一个具体的例子，但是您可以想象任何其他涉及通过不可靠信道与不可靠服务通信的星座。

## 重试

每当我们认为可以通过再次发送请求来修复意外响应(或者没有响应)时，使用重试模式会有所帮助。这是一个非常简单的模式，在操作被标记为失败之前，失败的请求在失败的情况下被重试可配置的次数。

下面的动画演示了支付服务试图签发欺诈支票。由于欺诈检查服务中的内部服务器错误，第一个请求失败。支付服务重试该请求，并接收交易不是欺诈性的回答。

[![](img/410bf1dde3ab992649ef383244c1a182.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UD_Gry29--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o00e224x9qf4g100kcax.gif)

重试在以下情况下会很有用

*   数据包丢失等暂时性网络问题
*   目标服务的内部错误，例如由数据库中断引起的错误
*   由于对目标服务的大量请求，没有响应或响应缓慢

但是请记住，如果问题是由目标服务过载引起的，重试可能会使问题变得更糟。为了避免将您的弹性模式转变为拒绝服务攻击，重试可以与其他技术结合使用，如指数退避或断路器(见下文)。

## 回退

回退模式使您的服务能够在对另一个服务的请求失败时继续执行。我们填充一个回退值，而不是因为缺少响应而中止计算。

下面的动画再次描述了支付服务向欺诈检查服务发出请求。同样，欺诈检查服务返回一个内部服务器错误。然而，这一次我们有一个后备方案，假设交易不是欺诈性的。

[![](img/4e66ccab6c84aa405e9f0cde04a5666a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MRhfRPLU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7oxihrr3n5k67h5h4szb.gif)

回退值并不总是可行的，但如果小心使用，可以大大提高您的整体弹性。在上面的例子中，在欺诈检查服务不可用的情况下，退回到将交易视为非欺诈可能是危险的。它甚至为欺诈交易打开了攻击面，欺诈交易试图首先向服务发送垃圾邮件，然后进行欺诈交易。

另一方面，如果后退是假设每个交易都是欺诈性的，则没有支付会通过，并且后退基本上是无用的。一个好的折衷办法可能是退回到一个简单的业务规则，例如，简单地让合理的小额交易通过，以便在风险和不失去客户之间取得良好的平衡。

## 超时

超时模式非常简单，许多 HTTP 客户端都配置了默认超时。目标是避免无限的响应等待时间，从而在超时内没有收到响应的情况下将每个请求视为失败。

下面的动画显示了支付服务等待欺诈检查服务的响应，并在超时后中止操作。

[![](img/96151fda0b5b880e6d799dd36f41d1bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--29ERQzjI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e0eetjip5m311u9ofpzr.gif)

几乎每个应用程序都使用超时来避免请求永远被阻塞。然而，处理超时并不简单。想象一下网上商店的订单超时。您无法确定订单是否已成功下达但响应超时，订单是否仍在创建中，或者请求是否从未得到处理。如果您将超时与重试相结合，您可能会得到重复的订单。如果您将订单标记为失败，客户可能会认为订单没有成功，但实际上可能成功了，他们将被收取费用。

此外，您希望您的超时足够高，以允许较慢的响应到达，但又足够低，以停止等待永远不会到达的响应。

## 断路器

在电子学中，断路器是一种保护你的元件免受过载损坏的开关。在软件中，断路器保护您的服务免受垃圾邮件，同时由于高负载已经部分不可用。

Martin Fowler 描述了[断路器](https://martinfowler.com/bliki/CircuitBreaker.html)模式。它可以实现为有状态的软件组件，可以在三种状态之间切换:关闭(请求可以自由流动)、打开(请求被拒绝而不提交给远程资源)和半开(允许一个探测请求来决定是否再次关闭电路)。下面的动画展示了一个运行中的断路器。

[![](img/5fe33ee55c270a132e4a8ec219c29ea0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--27Vzd1lb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u3f6z484i74k0osz5r6z.gif)

从支付服务到欺诈检查服务的请求通过断路器传递。两次内部服务器错误后，电路断开，后续请求被阻塞。等待一段时间后，电路进入半开状态。在这种状态下，它将允许一个请求通过，并在失败时变回打开状态，或者在成功时变回关闭状态。下一个请求成功，因此电路再次闭合。

断路器是一个有用的工具，尤其是在与重试、超时和回退结合使用时。后退不仅可以在故障情况下使用，也可以在电路开路的情况下使用。在下一节中，我们将看看用 Kotlin 编写的 Vert.x 代码示例。

# 在 Vert.x 中实现

在上一节中，我们从理论的角度看了不同的弹性模式。现在让我们看看如何实现它们。示例的[源代码](https://github.com/FRosner/vertx-kotlin-resilience-example)可以在 GitHub 上获得。在这个展示中，我们将使用 Vert.x 和 Kotlin。其他选择将在下一节讨论。

Vert.x 提供了 [`CircuitBreaker`](https://vertx.io/docs/vertx-circuit-breaker/java/) ，一个强大的装饰类，支持重试、回退、超时和断路器配置的任意组合。您可以使用如下所示的`CircuitBreakerOptions`类配置断路器。

```
val vertx = Vertx.vertx()
val options = circuitBreakerOptionsOf(
    fallbackOnFailure = false,
    maxFailures = 1,
    maxRetries = 2,
    resetTimeout = 5000,
    timeout = 2000
)
val circuitBreaker = CircuitBreaker.create("my-circuit-breaker", vertx, options) 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们将创建一个断路器，该断路器在将操作视为失败之前会重试两次。一次故障后，我们打开电路，5000 毫秒后电路将再次半开。2000 毫秒后操作超时。如果指定了回退，则仅在电路开路时调用。即使电路闭合，也可以将断路器配置为在出现故障时调用回退。

为了执行一个命令，我们需要提供一段异步代码来执行类型`Handler<Future<T>>`以及类型`Handler<AsyncResult<T>>`的处理程序来处理结果。一个返回`OK`并在之后打印出来的最小示例如下:

```
circuitBreaker.executeCommand(
    Handler<Future<String>> {
        it.complete("OK")
    },
    Handler {
        println(it)
    }
) 
```

Enter fullscreen mode Exit fullscreen mode

当在 Kotlin 中使用 [Vert.x 时，你也可以将挂起函数作为参数传递，而不是使用处理程序。请参见](https://blog.codecentric.de/en/2019/02/vert-x-kotlin-coroutines/) [`CoroutineHandlerFactory`](https://github.com/FRosner/vertx-kotlin-resilience-example/blob/master/src/main/kotlin/de/frosner/vkre/CoroutineHandlerFactory.kt) 类及其用法。除了这些基本功能，Vert.x 断路器模块还提供以下高级功能:

*   **事件总线通知。**断路器可以在每次状态改变时向事件总线发布事件。如果您想以某种方式对这些事件做出反应，这是很有用的。
*   **指标。**断路器可以发布由 Hystrix 仪表板使用的指标，以可视化断路器的状态。
*   **状态改变回调。**您可以配置电路打开或关闭时调用的自定义处理程序。

# 替代实现方法

不是每个框架都支持现成的弹性设计模式。此外，Vert.x 并不支持所有可能的模式。有直接解决弹性主题的指定项目，如 [Hystrix](https://github.com/Netflix/Hystrix) 、 [resilience4j](https://github.com/resilience4j/resilience4j) 、 [failsafe](https://github.com/jhalterman/failsafe) ，以及 [Istio](https://github.com/istio/istio) 的弹性特性。

Hystrix 已用于许多应用，但已不再处于积极开发阶段。Hystrix、resilience4j 和 failsafe 都是从应用程序源代码中直接调用的。例如，您可以通过实现接口或使用注释来集成它。

另一方面，Istio 是服务网格，因此是基础设施的一部分，而不是应用程序代码的一部分。它用于编排分布式服务系统，并实现了 [sidecar](https://docs.microsoft.com/en-us/azure/architecture/patterns/sidecar) 的概念。服务通信是通过 sidecar 进行的，sidecar 是服务流程旁边的一个专用流程。sidecar 然后可以处理诸如重试之类的机制。

sidecar 方法的优点是您不会将业务逻辑与弹性逻辑混淆。您可以替换 sidecar 技术，而无需接触太多的应用程序代码。此外，您可以轻松地修改和适应 sidecar 配置，而无需重新部署服务。缺点在于不能使用实现特定的模式，例如线程池隔离的[隔板](https://docs.microsoft.com/en-us/azure/architecture/patterns/bulkhead)模式。此外，回退值等模式严重依赖于您的业务逻辑。扩展现有的代码库可能比添加新的基础设施组件更容易。

# 总结

在这篇文章中，我们看到了松散耦合、隔离、延迟控制和监督是如何积极影响系统弹性的。重试模式能够处理通信错误，这些错误可以通过多次尝试来纠正。回退模式有助于在本地解决通信故障。超时模式提供了延迟的上限。断路器解决了由于在持续通信错误的情况下重试和快速后退而导致的意外拒绝服务攻击的问题。

像 Vert.x 这样的框架提供了一些现成的弹性模式。还有专门的弹性库，可以用于任何框架。另一方面，服务网格作为在基础设施层面引入弹性模式的一种选择而存在。一如既往，没有一个放之四海而皆准的解决方案，你的团队应该找出最适合他们的方案。