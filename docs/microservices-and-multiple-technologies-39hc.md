# 微服务和多种技术

> 原文：<https://dev.to/tonyhicks20/microservices-and-multiple-technologies-39hc>

在更小的、[可独立部署的](https://dev.to/tonyhicks20/microservices-and-multiple-delivery-streams-35g4)和[隔离模块](https://dev.to/tonyhicks20/microservices-and-enforced-modularity-b1h)中运行您的系统允许您使用任何最适合您的问题的技术。

由于每个服务都是自包含的，并且唯一可以与之通信的方式是通过服务的公共定义接口，这意味着公共接口后面发生的几乎所有事情都是您的服务的唯一业务！因此，从理论上来说，你可以自由地使用你那颗渴望科技的心想要的任何科技。

## 为什么要这么做？

在软件世界中有一个非常(我认为)被过度使用的表达是这样的:“如果你只有一把锤子，那么一切看起来都像钉子”。

有时，您在尝试做的工作中使用了不正确的工具。然而，在一个完整的代码库中，很难进行修改，因为你可能会发现你需要一次修改所有的代码，这样做太冒险和耗时。由于**微**服务很小，您可以通过改变系统的一部分的底层技术，同时保持系统的其余部分不变来进行试验。然后，您可以在您的试运行环境中对此进行彻底的测试，并确定它是否比现有技术做得更好。

这不一定只是对尝试新技术和解决技术问题有好处，但它也可以通过扩大从 T1 雇佣的开发人员来帮助你的业务。

例如，如果您的公司只做后端 Java，但是您的公司很难找到和雇用优秀的 Java 开发人员，并且公司已经看到市场上有很多优秀的 python 开发人员，那么您可以，再一次- **理论上**，现在开始雇用 Python 开发人员！

<figure>

[![Mind Blown](img/34b8db6e6addfa98f0280ed9eb1ae688.png)](https://i.giphy.com/media/xT0xeJpnrWC4XWblEk/giphy.gif)

<figcaption>Coming to an HR department near you!</figcaption>

</figure>

## 这其中的潜在问题

使用新技术——不管是新的框架、语言还是数据库——比你坐在电脑前学习使用它要多得多。

会有一些奇怪的事情，隐藏的陷阱会在你最意想不到的时候出现。篱笆另一边的草不总是更绿。风险值得潜在的回报吗？

<figure>

[![Look at the grass](img/561ae4bc3ccaf1e32a3873624a7f78f0.png)](https://i.giphy.com/media/GCjIE9C9Eb9V6/giphy.gif)

<figcaption>Is the grass greener because it's fake?</figcaption>

</figure>

其他学习新技术的人呢？其他开发人员愿意投入时间和精力来熟悉它吗？

保养怎么样？许多公司都有熟悉他们使用的数据库类型的 DBA。给公司增加一个新成员也需要他们的参与。

下游报道呢？新数据库将如何适应现有的业务流程？

为了取得成功，向您的系统添加新技术将需要您的组织进行一些潜在的重大转变！

## 有选择权真好

即使你从未为某项特定服务使用不同的技术，如果需要的话，能够这样做还是很好的！即使将来该系统成为遗留系统并需要重写，至少现在可以在孤立的部分中完成，而不是大型企业系统通常需要的大爆炸方法。