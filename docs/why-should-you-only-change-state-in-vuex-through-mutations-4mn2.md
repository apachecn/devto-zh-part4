# 为什么在 Vuex 中只能通过突变来改变状态？

> 原文：<https://dev.to/firstclown/why-should-you-only-change-state-in-vuex-through-mutations-4mn2>

这是我经常遇到的一个问题，也是我时常对自己感到疑惑的一个问题。使用 Vuex 时，为什么说状态只能通过突变来改变？这真的有那么重要吗？我不能直接改吗？这样看起来确实很好。

嗯，是的。不。也许。虽然在 Vuex 中可以在突变之外随意改变状态，但它也可能在你最意想不到的时候严重失败。

数据存储的[流动风格](https://github.com/facebook/flux/tree/master/examples/flux-concepts)(像 Vuex)的想法是数据有一个特定的路径流动。首先，它命中一个动作，然后被传递给一个变异，然后才在存储中被更新。这样做是为了使数据存储的调试和使用更容易，而不是疯狂制作。如果所有的组件都不情愿地将数据扔进`state`中，那么所有的混乱都会爆发。JavaScript 是一种异步语言，程序的两个不同部分可能同时试图改变同一件事。读取数据？那很好。写吗？你手上有个问题。

至少理论上是这样。我写了一些组件试图打破这一点，但我还不能让它发生。这主要是因为 JavaScript 在理论上是异步语言。实际上，大多数代码将同步运行，JavaScript 将等待一个函数完成后再运行下一个函数。因此，即使两个函数同时被触发(比如超时或共享事件)，其中一个总是先运行，然后是另一个。

这意味着突变毕竟不是真正的一致性特征。那么它们真的还有用吗？

是的，因为它仍然给了你一个简单的结构，让你在编写代码时可以遵循，一个你下次需要维护它时可以记住的结构。如果你遵循*总是*用一个突变改变状态的惯例，那么在你的应用程序中你将总是知道状态在哪里被改变。

突变由组件和动作调用，但不像常规方法。他们变得“忠诚”。这仅仅意味着当一个组件想要改变数据时，该改变被添加到数据存储中的提交队列中，并且它是先来先服务的。事情不会同时发生，因为 Vuex 以同步接收的顺序处理变化。

这使得调试更加容易。在你意想不到的地方看到奇怪的数据？打开 Vue 开发工具，一步一步地修改数据存储。对数据存储的每个更改都将被“提交”,您将能够回滚并查看导致该点的所有内容。这种日志记录是您应该只通过突变来改变状态的真正原因。