# 去耦 Laravel 码

> 原文：<https://dev.to/programmingdecoded/decoupling-laravel-code-3383>

最近我一直在读两本书，这两本书提供了关于如何创建干净的可维护的 Laravel 代码的有用想法。

分别是:

1)[Kristopher Wilson

的《PHP 中的干净架构》2)](https://leanpub.com/cleanphp)[Adel F](https://leanpub.com/architecture-of-complex-web-applications)

克里斯多佛·威尔逊的第一个是基于罗伯特·c·马丁的“清洁建筑”。基本的信息是 MVC 对于复杂的 web 应用程序来说不是一个好的模式。

<figure>[![](img/3b2eb715ecebeb85b1bdf77068a158d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xpTb-Z6d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.programming-decoded.com/wp-content/uploads/2019/06/messy_mvc.png) 

<figcaption>凌乱的 MVC</figcaption>

</figure>

干净的架构提供了另一种选择。

[![](img/664abaa685db4b5bb6182f7163fd323f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ncr8mwNs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.programming-decoded.com/wp-content/uploads/2019/06/clean_architecture.jpg)

在这篇博文中，我不会描述干净架构的所有元素(最好阅读这本书)，而是总结了我喜欢这本书的一些地方。作者首先描述了糟糕的架构是什么样子，然后提供了我们如何解耦代码的各种方法。这些包括:使用设计模式，利用可靠的设计原则，使用依赖注入和用接口定义契约。我实际上发现这本书的这部分是最有帮助的部分。第三部分描述了 MVC 的局限性以及如何使用 clean 架构。这是一个进步，但是值得庆幸的是，在剩余部分有一个案例研究，作者创建了各种干净的架构元素，如实体和域服务。然后，他给出了一个例子，说明它们是如何集成到 Zend 框架中的，以及由于代码现在是独立于框架的，将代码移植到 Laravel 是多么简单。你可以在这里找到代码:[https://github.com/mrkrstphr/cleanphp-example/tree/master](https://github.com/mrkrstphr/cleanphp-example/tree/master)

对我来说，框架独立性部分有点极端，但书中有很多概念我都尝试在自己的项目中实现。尽管如此，我仍然试图找到正确的平衡，因为我发现过多的抽象也会导致代码更难理解。

总的来说，我发现我正在读的关于这个主题的第二本书“复杂 web 应用程序的架构”更实用一些。它还有一章是关于依赖注入的，比第一本书更详细地解释了它是如何完成的。当谈到“应用层”时，它给出了实用的技巧，例如如何处理 http 请求。所解释的在层间传输数据的模式是使用数据传输对象(DTO)。

我还喜欢添加静态类型的技巧，这对于在 IDE 中实现自动完成非常有用。遗憾的是，一些技巧，比如在刀片模板中使用 phpDoc，只适用于 PhpStorm。

我为第二本书错过的一件事是，我在 Github 上找不到代码示例。尽管这本书附带了大量代码，但是要理解这些代码作为一个整体是如何工作的还是很困难的。

我还在继续写第二本书，可能我也会重读第一本书，所以你可能会期待更多关于这个话题的博客帖子。