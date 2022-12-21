# 从优秀到卓越的模式

> 原文：<https://dev.to/rposener/good-to-great-with-patterns-i5d>

# 项目模式

每个项目似乎都有一个模式。在。NET 世界中，我们通常从某种形式的 MVC 开始。如果你在做微服务，那么休息就成了团队达成一致的首要任务。在一些项目中，它是 DDD。我觉得无论是团队还是项目，团队都倾向于建立一个模式声明，比如:

> 我们在这个项目上遵循 ________ 模式。

有一个团队谈论模式是很棒的。代码看起来整洁有序，对新的 jr. devs 来说很有意义。他们期待中级和高级开发人员乐意回答他们关于 _____ 模式的丰富经验中的问题。那些开发人员讲述了关于 it 出错的故事，以及比这支团队做得更好的团队的经历。该团队以这种模式工作而自豪。

> 这些都是好 dev！这是一个很好的团队。

# 图案有问题

在我参与的所有应用程序中，90%以上都使用了不止一种模式。图案相互交叉，相交，编织成代码。但是关于 1 模式的讨论太多了，一切都围绕着它。其他模式仅仅是事后的想法。问题就这样开始了...

## 举个例子

以 MVC 开始构建微服务架构的团队专注于 REST 端点，使得一切都基于 REST。每个元素都有自己的端点，支持 GET、POST、PUT、DELETE，偶尔还会为遇到的每个对象打补丁。

他们的应用程序正在处理对每个页面视图的微服务的 50 个请求，以建立视图，事情开始变得缓慢。

有人建议使用缓存服务在一次请求中返回一个“复合”对象来加快速度。这是微服务，所以为什么不添加另一个查询服务来完成呢？好主意——团队给了他们自己计算机科学中最难的两个问题。

## 自己错过了什么

因此，一个新人从 DDD 培训中直接加入，并敢于问“你为什么不使用 DDD 并利用一个好的聚合根”。该代码的 99%从这两个根请求一切，并执行与这些根一致的操作。

当然，在这一点上，团队很快放弃了这个想法，因为这不是我们正在工作的 REST 模式。毕竟，这就是微服务应该做的。

# 外卖

对于任何项目，保持对新模式的关注。敏锐地理解不同模式在代码中的位置。

> 这将一个好的开发者变成一个伟大的开发者。

在你编码的时候，不断地问自己这样的问题。

*   JS 代码是跟在 [MVVM](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel) 后面吗？
*   服务层中是否有某个方面的代码会受益于完整的 [DDD](https://dddcommunity.org/) 方法？
*   存储库模式是一致的吗，或者它们只是分裂和混淆一个集合根？
*   是否有一个 [n 层](https://en.wikipedia.org/wiki/Multitier_architecture)模式，如果使用的话，我们的[工作单元](https://www.thereformedprogrammer.net/is-the-repository-pattern-useful-with-entity-framework-core/)模式是如何工作的？
*   状态转移 [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) 是否适合您的应用程序，或者使用 [RPC](https://en.wikipedia.org/wiki/Remote_procedure_call) 风格的方法是否更合理？

注意:[这篇关于 REST 混淆的文章](https://www.smashingmagazine.com/2016/09/understanding-rest-and-rpc-for-http-apis/)非常值得一读，并且详细介绍了更多具体的例子。