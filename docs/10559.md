# 从节点服务器发出一堆请求？试试诺言. all！

> 原文：<https://dev.to/jacobknaack/making-a-bunch-of-requests-from-a-node-server-try-promise-all-23fl>

有时您会发现自己需要发出大量的 http 请求。在很大程度上，这是一个坏主意，你真的应该抽象出你的请求，而不是敲打一个 REST API，因为那是你在互联网上破坏东西的方式。

但是为了测试的目的，或者只是想让一些该死的东西工作，我们可能会感到有点不舒服。你知道，我们是开发商，女孩们只是想玩得开心！

[![hack-a-lackin](img/883aa00e156305cd6a9a9f22b57dee0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MzsthIM2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.makeagif.com/media/10-24-2015/KP9C6v.gif)

## 免责声明:

> 请不要在任何你无法控制的现有 REST 服务上使用它。这可能会打破服务已经设置任何速率限制，更糟糕的情况可能会使他们的服务器过载。

在测试一堆模拟电子表格数据时，我发现自己处于其中一个场景中，我想向我们团队正在构建的一些服务器路由发出数百个请求。因此，这个节点代码的出现就在你的下面。

在继续之前，这个黑客需要一些关于 JS 承诺、ES6 语法和节点模块的知识。现在，让我们来看看这个解决方案，并对其进行分解: