# 好莱坞控制原则投资公司:“不要打电话给我们，我们会打电话给你”

> 原文：<https://dev.to/mangelsnc/inversion-de-control-principio-de-hollywood-don-t-call-us-we-ll-call-you-2a43>

[![Don’t call us, we’ll call you](img/4d7f46c129150d02eff1ed1c6422928d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zwbMGIHD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4016/1%2A6Tc3M_8zEYKoQSxdwyzicg.jpeg)

### 导言

随着面向对象编程(POO)的兴起，应用程序代码从线性和可预测性转变为更复杂的流，在模块和组件之间进行多次跳跃，但吸引了两个主要优势:模块性和这些组件的重用。问题在于模块化带来了臂下一个越来越大的问题:模块之间的‘t0’依赖**。**

 **我们在其他场合谈到了模块和组件之间的相互依存关系，因此，我将不再详述这个问题。

当联检组的问题变得太大时，出现了两个新的设计概念:

*   控制投资(IoC)

*   从属关系注入(DI)

随着开发框架的出现，控制投资成为一种相当常见的开发形式，因为控制的是框架，定义了请求的行为流或生命周期，**框架是执行用户代码**

> # Control investment when the bookstore or framework runs user code

？的名字取自好莱坞选角导演常用的短语，以免接到应聘者的电话询问他们是否被录取。

### 国际奥委会

控制投资有许多使用案例，发展框架就是最明显的例子。

IoC 的其他主要用途是一些著名的设计模式:

*   [观察者](https://sourcemaking.com/design_patterns/observer)

*   [工厂方法](https://sourcemaking.com/design_patterns/factory_method)

*   [策略](https://sourcemaking.com/design_patterns/strategy)

*   [模板方法](https://medium.com/p/cba682d88dc5)

控制投资在代码的可扩展性方面起着相当重要的作用，因为除其他外，它使我们能够[扩展我们系统的功能而无需修改现有的类](https://medium.com/all-you-need-is-clean-code/principio-abierto-cerrado-ocp-6e34d387bb3a)

### 一个实际例子

大多数 PHP 框架为用户提供了自定义事件系统:symfony；[laravel；](https://laravel.com/docs/5.5/events)；[yi；](http://www.yiiframework.com/wiki/327/events-explained/)

事件系统基于 IoC 的原理，因为在订阅特定事件时，通常会收到两个参数:您订阅的事件和要运行的 callable。下面是 Symfony 事件调度程序的示例:

如果要通过在用户注册后执行更多操作来扩展应用程序功能，则创建一个新的监听程序并订阅该事件就相当简单了。不仅如此，我们还可以创建任意数量的自定义事件，并根据需要订阅任意数量的监听程序。

### 结论

IoC 每天都在现场，尽管我们并未意识到这一点，这有助于使我们的软件更具可扩展性、模块化、可重复使用和易于维护。

它长期存在于众所周知的桌面应用程序开发框架(如. NET)中，Java Spring 就是基于这一概念开发的，新的 web 框架(如 Symfony、rails…)也是基于这一概念开发的。

我希望这篇帖子有助于理解 IoC 的概念，因为我不久将开始撰写一系列关于设计模式、其应用以及如何反映我们的代码以实施这些模式的文章，我认为这是一个很好的话题，可以就此展开讨论；以及稍微加强上一篇文章中关于依存性投资和依存性注入的学习。

下次见！**