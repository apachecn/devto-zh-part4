# 微服务或只是云原生

> 原文：<https://dev.to/buildly/microservices-or-just-cloud-native-3ebl>

最近在 Buildly，我们经常问自己的一个问题是，我们是在做微服务，还是只是在做原生云？真的有区别吗？ [Google 似乎有一个观点](https://cloud.google.com/solutions/migrating-a-monolithic-app-to-microservices-gke)微服务架构几乎是高效使用云原生架构所必需的。这可能是对的，但我想这取决于你对“微”的定义。

我当然是微服务架构的粉丝，我想我们都同意它们在谷歌云平台上工作得最好，但我确实对“微”这个术语有问题。一旦你进入了无服务器架构，术语微(功能驱动和无状态架构)当然是合适的，但是当你需要构建由业务需求驱动的服务，并且开发者需要尽可能高效地构建那些模型和关系依赖时，也许微不是你应该考虑的方式。

也许这些容器驱动的服务更多的是业务逻辑服务，或者逻辑服务，或者类似的东西。过去我称它们为逻辑模块，但我不确定这是否足够解释它们是什么，当然模块意味着模块化和可重用，但我认为我们失去了一点微处理器会给我们的正确大小或比例。

当然，可重用服务在描述这些服务时对我们来说是有意义的，但是并不是每个软件开发过程都需要关注代码的可重用性。也许他们应该，我知道这是我开始开发软件以来学到的一个基本原则，但有时企业有一个简单的需求，一个简单的整体式架构更适合这种需求，即使整体式架构在服务规模方面可能不完全准确。对于只关心构建一个产品的经理或 CEO 来说，在可重用性上花费时间的价值是不容易论证的。我认为这里的答案必须是支持最佳实践和标准，以表明更小的模块化代码，不管重用与否，在需要时更容易维护、优化和修复。这也有助于更快地开发代码，更容易地在不同的团队之间传播测试，并有助于减少冗余。

目前，也许我们能做的最好的事情是让这些术语更加同义，云原生和微服务架构一起工作会更好，也许只有一起工作。如果我们将术语“微”从我们正在构建的服务中去掉，它不会对我们造成太大的限制，并且它使我们能够基于业务逻辑、开发人员职责和逻辑功能分离以及状态管理的需要来清楚地分离这些服务。有些人又回到了面向服务的架构这个术语，虽然我一直喜欢这种方法，但它现在看起来像是一个载入历史的术语，是一种倒退。我似乎总是回到逻辑服务或逻辑驱动的服务。

也许最终我们也会通过减少对状态的关注，更多地关注结构和执行，来弥补面向对象和函数式编程之间的一些差距。无论哪种方式，评估这些实践来寻找优化的方法都不会有什么坏处。最后，这些都只是术语，对我来说更多的是方法，但我想我们需要一种精确的方式来沟通。现在，我想我们将坚持逻辑服务，并解释其区别。

如果您感兴趣，我们已经整理了[一份介绍，介绍我们如何通过网关](https://buildly.io)功能和我们的云原生服务和模式来管理这些服务，并且很快将开始添加服务的逻辑分离和一些关于我们如何围绕模型实现标准化的想法。