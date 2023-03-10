# 编写干净代码的十大面向对象设计原则

> 原文：<https://dev.to/javinpaul/top-10-object-oriented-design-principles-for-writing-clean-code-4pe1>

*披露:这篇文章包括附属链接；如果您从本文提供的不同链接购买产品或服务，我可能会收到报酬。*

面向对象的设计原则是 OOP 编程的核心，但我看到大多数 Java 程序员追逐设计模式，如[单例模式](https://javarevisited.blogspot.com/2014/05/double-checked-locking-on-singleton-in-java.html)、[装饰模式](http://www.java67.com/2013/07/decorator-design-pattern-in-java-real-life-example-tutorial.html)或[观察者模式](http://javarevisited.blogspot.sg/2011/12/observer-design-pattern-java-example.html)，而没有将足够的注意力放在学习*面向对象的分析和设计*上。

学习面向对象编程的基础知识很重要，比如[抽象](https://javarevisited.blogspot.com/2017/04/difference-between-abstraction-and-encapsulation-in-java-oop.html)、[封装](https://javarevisited.blogspot.com/2012/03/what-is-encapsulation-in-java-and-oops.html)、[多态](http://javarevisited.blogspot.sg/2011/08/what-is-polymorphism-in-java-example.html)和[继承](http://www.java67.com/2016/03/top-21-java-inheritance-interview-Questions-Answer-Programming.html)。但是，与此同时，了解面向对象的设计原则也同样重要。

> 它们将帮助您创建一个清晰的、模块化的设计，在将来易于测试、调试和维护。

我经常看到不同经验水平的 Java 程序员和开发人员，他们要么从未听说过这些 [OOP](http://www.java67.com/2015/12/top-30-oops-concept-interview-questions-answers-java.html) 和[可靠的设计原则](https://pluralsight.pxf.io/c/1193463/424552/7490?u=https%3A%2F%2Fwww.pluralsight.com%2Fcourses%2Fprinciples-oo-design) **，**，要么根本不知道某个特定的设计原则提供了什么好处以及如何在编码中应用这些设计原则。

为了尽我所能，我已经记下了所有重要的面向对象设计原则，并放在这里以供快速参考。这些至少会让你对他们是什么和他们提供什么好处有所了解。

我没有放例子，只是为了使文章简短，但你可以在互联网上甚至在我的 [**Java 博客**](http://javarevisited.blogspot.com/) 上找到很多这些设计原则的例子，只需使用页面顶部的搜索栏。

如果你不能理解一个设计原则，你应该尝试做更多的例子，因为有时我们会更好地联系到另一个例子或作者，但你必须理解这些设计原则，并学习如何在你的代码中使用它。

你可以做的另一件事是参加一个综合性的面向对象设计课程，像史蒂夫·史密斯在 Pluralsight 上的 [**【面向对象设计的坚实原则】**](https://pluralsight.pxf.io/c/1193463/424552/7490?u=https%3A%2F%2Fwww.pluralsight.com%2Fcourses%2Fprinciples-oo-design) 。这对我理解和应用这些原则帮助很大。

> 顺便说一句，我也分享了相关和有用的课程和书籍，无论是免费的还是付费的，如果你买一些不是免费的东西，我会赚一些钱。它们是我用来学习坚实的设计原则和一般编程的资源，对深入学习这些原则非常有用。

## 10 OOP &编写干净代码的坚实设计原则

虽然学习任何设计原则或模式的最佳方式是一个真实世界的例子，并理解违反该设计原则的后果，但本文的主题是为 Java 程序员介绍*面向对象的设计原则，这些程序员要么没有接触过它，要么正处于学习阶段。*

 *[![](img/44d0a756b87c8baecdc020b2fde4ca8c.png)](https://javarevisited.blogspot.com/2018/07/10-object-oriented-design-principles.html#axzz5vGglR1hx)

我个人认为这些 [OOP](https://medium.com/swlh/5-free-object-oriented-programming-online-courses-for-programmers-156afd0a3a73) 和[坚实的设计原则](https://javarevisited.blogspot.com/2018/02/top-5-java-design-pattern-courses-for-developers.html)中的每一个都需要一篇文章来解释清楚，我肯定会在这里尝试这样做，但是现在，你只需要准备好在设计原则小镇上快速骑自行车:)

### 1。干(不重复)

我们第一个面向对象的设计原则是 DRY，顾名思义 **DRY(不要重复自己)**的意思是不要写重复的代码，而是用[抽象](http://javarevisited.blogspot.com/2010/10/abstraction-in-java.html)把常见的东西抽象在一个地方。

如果你在两个以上的地方有一个代码块，考虑让它成为一个单独的方法，或者如果你不止一次使用硬编码值，让它们成为 [public final constant](http://javarevisited.blogspot.com/2011/12/final-variable-method-class-java.html) 。这种面向对象设计原则的好处在于**维护**。

> 重要的是不要滥用它，复制不是为了代码，而是为了功能。

这意味着，如果您使用通用代码来验证订单 Id 和 SSN，这并不意味着它们是相同的，或者它们在未来将保持不变。

[![](img/ea25c600729ed8a68dc86bf870e31422.png)](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=562016.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Fbasics-of-software-architecture-design-in-java%2F)

通过对两个不同的功能或事物使用公共代码，您将它们永远紧密地耦合在一起，当您的 OrderId 改变其格式时，您的 SSN 验证代码将会中断。

所以要小心这种耦合，不要组合任何使用相似代码但不相关的东西。你可以进一步查看 Java 课程中的 [**软件架构基础&设计模式**](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=562016.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Fbasics-of-software-architecture-design-in-java%2F) ，了解更多关于编写好代码和设计系统时遵循的最佳实践。

你也可以阅读迪夫·托马斯和安德鲁·亨特的经典著作《实用程序员》，该书首次创造了这个术语，并解释了“干”的确切含义

### 2。封装什么变化

在软件领域只有一件事是不变的，那就是“改变”，所以，封装你预期或怀疑将来会改变的代码。

> 这种 OOP 设计原则的好处是很容易测试和维护适当的封装代码。

如果你用 Java 编程，那么遵循这样的原则:默认情况下，将变量和方法设为私有，逐步增加访问权限，比如从私有到受 T2 保护的，而不是公开的。

[![](img/10120c8add7ac7f3a270533c5bc778f6.png)](https://pluralsight.pxf.io/c/1193463/424552/7490?u=https%3A%2F%2Fwww.pluralsight.com%2Fcourses%2Fpatterns-library)

Java 中的几个**设计模式使用封装，[工厂设计模式](http://javarevisited.blogspot.com/2011/12/factory-design-pattern-java-example.html)就是封装的一个例子，它封装了对象创建代码，并提供了在不影响现有代码的情况下引入新产品的灵活性。**

顺便说一句，如果你有兴趣学习更多关于 Java 和面向对象编程中的设计模式，那么你必须查看这个 [**设计模式库**](https://pluralsight.pxf.io/c/1193463/424552/7490?u=https%3A%2F%2Fwww.pluralsight.com%2Fcourses%2Fpatterns-library) 课程 Pluralsight。这是设计模式和如何在现实世界中使用它们的建议的最佳集合之一。

### 3。开放封闭设计原则

根据这种 OOP 设计原则，“类、方法或函数应该对扩展(新功能)开放，对修改关闭”。

这是另一个漂亮可靠的设计原则，由 Bob 叔叔在他经典的 [**干净代码**](http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882?tag=javamysqlanta-20) 一书中提出，防止有人改变已经尝试和测试过的代码。

> 这种设计原则的主要好处是已经尝试和测试过的代码不会被触及，这意味着它们不会被破坏。

下面是一个 Java 代码示例，*违反了*编程的开放封闭设计原则:

[![](img/3bdd74492d0b240e5655ac27c1946ada.png)](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fbasics-of-software-architecture-design-in-java%2F)

在这段代码中，GraphicEditor 与 Shape 紧密耦合，如果您需要一个新的形状，那么您需要修改已经在`drawShape(Shape s)`方法中尝试和测试过的代码，这既容易出错，也不可取。

理想情况下，如果你只是添加新功能，那么你的代码应该被测试，这就是[开放封闭设计原则](http://javarevisited.blogspot.com/2011/11/great-example-of-open-closed-design.html)的目标。

顺便说一下，开闭原则是“O”从固体缩写。如果你想进一步了解这个原则，Udemy 上的 [**面向对象设计和架构的坚实原则**](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fsolid-principles-object-oriented-design-architecture%2F) 课程是最好的参考资源之一。

### 4。单一责任原则

单一责任原则是另一个可靠的设计原则，在可靠的首字母缩略词上代表“S”。按照 SRP，一个类的改变不应该有一个以上的原因，或者一个类应该总是处理单一的功能。

> 这个原则的主要好处是它减少了软件和代码的单个组件之间的耦合。

例如，如果你在 Java 的一个类中放入多个功能，它会在两个功能之间引入**耦合**，即使你改变一个功能，也有可能破坏耦合的功能，这需要另一轮测试，以避免在生产环境中出现任何意外。

你可以在 Udemy 上的 Dmitri Nestruk 的 Java 课程中进一步查看 [**设计模式，了解基于这一原则的模式。**](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fcourse%2Fdesign-patterns-java%2F)

[![](img/75b5d905e840376a27b85d788856e675.png)![](img/8e7940f26a061ec4c6fcdbfa670a05ab.png)T3】](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=323058.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Ffrom-0-to-1-design-patterns%2F)

### 5。从属倒置原则

不要问依赖关系，它会由框架提供给你。这在 Spring framework 中得到了很好的实现，Spring framework 是编写真正有价值的应用程序的最流行的 Java 框架之一。

> 这种**设计原则**的美妙之处在于，由 DI 框架注入的任何类都**易于用模拟对象进行测试**，并且更易于维护，因为对象创建代码集中在框架中，客户端代码不会因此而杂乱无章。

实现**依赖注入有多种方式，比如使用字节码插装，像 AspectJ 这样的 AOP(面向方面编程)框架就是这样做的，或者使用代理，就像在 [Spring](https://medium.com/javarevisited/10-best-spring-framework-books-for-java-developers-360284c37036) 中使用的那样。**

 **它也代表固体缩写词“D”。

下面是一个代码示例，它违反了 Java 中的依赖倒置原则或 DIP:

[![](img/f04064d38915b184319d9cdae13371d3.png)](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fsolid-principles-object-oriented-design-architecture%2F)

你可以看到`AppManager`依赖于`EventLogWriter`，而`EventLogWriter`与`AppManager`紧密耦合。如果您需要使用另一种方式通知您的客户端，比如发送推送通知、SMS 或电子邮件，您需要更改`AppManager`类。

这个问题可以通过使用依赖倒置原则来解决，其中代替`AppManager`请求`EventLogWriter`，它将由框架注入或提供给 AppManager。

您可以进一步查看[使用可靠的原则编写更好的代码 Udemy 上的速成班](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2F1-hour-crash-course-using-solid-to-write-better-code%2F),了解更多关于依赖倒置原则以及如何解决此类问题的信息。

### 6。偏爱合成而非遗传

在 OOP 中，有两种通用的方法来重用你已经写好的代码，继承和组合，这两种方法各有优缺点，但是，一般来说，如果可能的话，你应该总是倾向于组合而不是继承。

> 你们中的一些人可能会争论这一点，但是我发现[合成](https://javarevisited.blogspot.com/2014/02/ifference-between-association-vs-composition-vs-aggregation.html#axzz5bnktTXS4)比[继承](http://javarevisited.blogspot.sg/2012/10/what-is-inheritance-in-java-and-oops-programming.html)灵活得多。

通过在运行时设置属性和使用接口来组合一个类，组合允许在运行时改变一个类的行为。我们使用[多态](http://javarevisited.blogspot.com/2011/08/what-is-polymorphism-in-java-example.html)来提供在任何时候用更好的实现替换的灵活性。

甚至约书亚·布洛赫的 [**有效 Java**](https://www.amazon.com/Effective-Java-3rd-Joshua-Bloch/dp/0134685997/?tag=javamysqlanta-20) 也建议支持复合而不是继承。如果你仍然不相信，那么你也可以阅读这里的[来了解更多关于为什么你的组合比继承更好的重用代码和功能。](http://javarevisited.blogspot.sg/2015/06/difference-between-inheritance-and-Composition-in-Java-OOP.html)

而且，如果你总是忘记这条规则，这里有一个很好的卡通放在你的桌子上:-)

[![](img/067b3cede51789729545c7da9e33610d.png)](https://coursera.pxf.io/c/3294490/1164545/14726?u=https%3A%2F%2Fwww.coursera.org%2Flearn%2Fobject-oriented-java)

如果你有兴趣学习更多的面向对象编程概念，如组合、继承、关联、聚合等，你也可以看看 [Coursera](https://coursera.pxf.io/c/3294490/1164545/14726?u=https%3A%2F%2Fwww.coursera.org%2F) 上的[Java 面向对象编程](https://coursera.pxf.io/c/3294490/1164545/14726?u=https%3A%2F%2Fwww.coursera.org%2Flearn%2Fobject-oriented-java)课程。

探索和学习是免费的，但如果你还想参与练习、作业、评估，并需要证明以显示在你的 LinkedIn 个人资料中，你将需要付费。

如果你参加这个课程是为了获得 Coursera 证书，那么你需要注册专业化或者参加一个订阅计划，比如 [**Coursera Plus**](https://coursera.pxf.io/c/3294490/1164545/14726?u=https%3A%2F%2Fwww.coursera.org%2Fcourseraplus) ，它提供 5000 多个 Coursera 课程、项目和专业证书的无限制访问。

这不是设计原则，而是在面向对象编程中编码的最佳实践。

### 7。利斯科夫替代原理

根据 Liskov 替换原则，子类型必须可以替换超类型，我的意思是使用超类类型的方法或函数必须能够毫无问题地使用子类的[对象](http://javarevisited.blogspot.com/2012/12/what-is-object-in-java-or-oops-example.html)。

LSP 与**单一责任原则**和**接口分离原则**密切相关。

> 如果一个类有更多的功能，那么子类可能不支持某些功能，并且违反 LSP。

为了遵循 [LSP 固体设计原则](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fsolid-principles-object-oriented-design-architecture%2F)，派生类或子类必须增强功能，而不是减少功能。LSP 代表实心首字母缩写词上的“L”。

下面是一个代码示例，它违反了 Java 中的 Liskov 替换原则:

[![](img/c2459a3501cfb79aba84b7dd9af5e5d0.png)](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fsolid-principles-object-oriented-design-architecture%2F)

Java 中的 Liskov 替换原理

违反 LSP 的原因是 Square 的行为与 Rectangle 的行为不匹配，例如，调用 setWidth 也会更改高度，而 Rectangle 则不是这种情况。

LSP 的另一个例子是 java.sql.Date，它违反了 Liskov 替换原则，因为即使 java.sql.Date 扩展了 java.util.Date，您也不能将其传递给期望 java.util.Date 的方法，因为所有与时间相关的方法(例如 getHour()、getMinute()和 getSeconds()方法)都会抛出 Java . util . notsupportedexception。

如果你对更真实的例子感兴趣，那么 Pluralsight 上的 [**面向对象设计的坚实原则**](https://pluralsight.pxf.io/c/1193463/424552/7490?u=https%3A%2F%2Fwww.pluralsight.com%2Fcourses%2Fprinciples-oo-design) 课程是一个很好的开始。

顺便说一句，你需要一个 Pluralsight 会员才能进入这个课程，费用大约是每月 29 美元或每年 299 美元(14%的折扣)。即使你没有会员资格，你也可以免费参加这个课程，你可以利用他们的 [**10 天免费试用期**](http://pluralsight.pxf.io/c/1193463/424552/7490?u=https%3A%2F%2Fwww.pluralsight.com%2Flearn) ，而不需要任何承诺，这不仅是免费参加这个课程的好方法，也是在加入 Pluralsight 之前检查课程质量的好方法。

### 8。接口隔离原则(ISP)

接口分离原则规定，如果客户端不使用接口,就不应该实现接口[。](http://javarevisited.blogspot.com/2012/04/10-points-on-interface-in-java-with.html)

这通常发生在一个接口包含多个功能，而客户端只需要一个功能而不需要其他功能的时候。

毫无疑问，界面设计是一项棘手的工作，因为一旦你发布了你的界面，你就不能在不破坏所有实现的情况下改变它。好吧，Java 8 的[默认或 defender 方法](https://javarevisited.blogspot.com/2014/07/default-defender-or-extension-method-of-Java8-example-tutorial.html#axzz5kO8vmDxy)特性确实提供了一种接口进化的方式，但是并不是所有的编程语言都支持这些特性。

> Java 中这种设计原则的另一个好处是，接口的缺点是在任何类可以使用它之前实现所有的方法，所以单一的功能意味着实现的方法更少。

如果你在编码中没有得到接口的好处，那么我建议你阅读我的博客文章，Java 中接口的真正用法。

[![](img/8b8a03af6d2d2a5da7d4025dd7f0aa5d.png)](http://javarevisited.blogspot.sg/2013/06/5-must-read-books-to-learn-object.html)

### 9。为接口而非实现编程

程序员应该始终*为接口编程，而不是为实现编程*这将产生灵活的代码，可以与接口的任何新实现一起工作。

具体来说，你应该在 [Java](https://medium.com/javarevisited/10-best-places-to-learn-java-online-for-free-ce5e713ab5b2) 中使用变量的接口类型、方法的返回类型或者方法的参数类型，比如使用`SuperClass`类型存储对象，而不是使用`SubClass`。

(解释或更正刚说过的话)我是说

`List numbers= getNumbers();`

代替

`ArrayList numbers = getNumbers();`

这在许多 Java 书籍中也有建议，包括在 [**有效 Java**](https://www.amazon.com/Effective-Java-3rd-Joshua-Bloch/dp/0134685997/?tag=javamysqlanta-20) 和[**Head First design pattern**](http://www.amazon.com/dp/0596007124/?tag=javamysqlanta-20)书籍中。

下面是一个用 Java 编写接口代码的例子:

[![](img/baac129871d7d35de02cff13a7526671.png)](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Frefactoring-to-patterns%2F)

如果你对提高程序的代码质量感兴趣，我也建议你看一下 Udemy 上的 [**重构到设计模式**](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Frefactoring-to-patterns%2F) 课程，它将帮助你用 C#中的重构技术和设计模式来改进内部设计。

### 10。授权原则

这是另一个有用的设计原则，也遵循职责分离逻辑。它说，不要一个人做所有的事情，把它委派给各自的班级。

委托设计原则的一个经典例子是 Java 中的 [equals()和 hashCode()](http://javarevisited.blogspot.com/2011/02/how-to-write-equals-method-in-java.html) 方法。

> 为了比较两个对象是否相等，我们要求类本身进行比较，而不是由`Client`类进行检查。

这种设计原则的主要好处是**没有重复代码**并且很容易修改行为。事件委托是这一原则的另一个例子，其中事件被委托给处理程序进行处理。

[![](img/f37d278aed285b0d38bd14f4e0f727e7.png)](https://medium.com/swlh/5-free-object-oriented-programming-online-courses-for-programmers-156afd0a3a73)

### 总结

所有这些**面向对象的设计原则**通过努力实现高内聚和低耦合，帮助你写出灵活且更好的代码。

理论是第一步，但最重要的是*培养发现何时应用这些设计原则的能力*。

一旦掌握了这一点，下一步就是学习 Java 中的设计模式，Java 使用这些设计模式来解决应用程序开发和软件工程中的常见问题。

如果你正在寻找一门不错的课程，我建议你加入 Udemy 上的 [**从 0 到 1:设计模式——24 个重要的——Java**](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=323058.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Ffrom-0-to-1-design-patterns%2F)课程。这是非常全面的，你可以在他们的几个 flash 销售中只花 11 美元就能买到。

无论如何，这里是所有这些 OOP 设计原则的一个很好的总结。

[![](img/933f8c9f3f1b299dc99ae547e44bcb05.png)](https://javarevisited.blogspot.com/2018/08/5-object-oriented-programming-and-design-courses-for-Java-programmers.html)

找出我们是否违反了任何设计原则和损害了代码的灵活性，但同样，在这个世界上没有什么是完美的，不要总是试图用**设计模式和设计原则**来解决问题，它们大多用于大型企业项目，维护周期较长。

底线是，专业程序员应该总是努力实现高度内聚和松散耦合的解决方案、代码或设计。查看 Apache 和 Google 的开源代码是学习 Java 和 T2 OOP 设计原则的好方法。

他们将向你展示，在编码和 Java 程序中应该如何使用设计原则。Java 开发工具包遵循许多设计原则，如`BorderFactory`类中的[工厂模式](http://javarevisited.blogspot.sg/2011/12/factory-design-pattern-java-example.html#axzz51cvxH5kW)、`java.lang.Runtime`类中的[单例模式](https://javarevisited.blogspot.com/2014/05/double-checked-locking-on-singleton-in-java.html)、各种`java.io`类中的[装饰模式](http://www.java67.com/2013/07/decorator-design-pattern-in-java-real-life-example-tutorial.html)。

如果你对学习面向对象的原则和模式感兴趣，那么你可以看看我的另一个个人最爱[**Head First Object-Oriented Analysis and Design**](http://www.amazon.com/dp/0596008678/?tag=javamysqlanta-20)，这是一本优秀的书，也可能是面向对象分析和设计方面最好的材料

[![](img/8d9c707f20187a5ca5b205668ea6633d.png)](http://www.amazon.com/dp/0596008678/?tag=javamysqlanta-20)

没有多少程序员知道这本书，因为它经常被更受欢迎的堂兄[T2 的](https://www.amazon.com/Head-First-Design-Patterns-Brain-Friendly/dp/0596007124/?tag=javamysqlanta-20)Eric Freeman 的【头先设计模式】所掩盖，这本书更多的是关于如何将这些原则结合在一起，创建一个你可以直接用来解决已知问题的模式。

[![](img/0ae906e2a7f95eeeb4299cfde375e4ce.png)](https://www.amazon.com/Head-First-Design-Patterns-Brain-Friendly/dp/0596007124/?tag=javamysqlanta-20)

这些书对编写更好的代码很有帮助，充分利用了各种面向对象和可靠的设计原则。顺便说一句，如果你真的对 Java 编码实践更感兴趣，那就去读读约书亚·布洛赫的《有效的 Java 第三版 ，这是写 Java 集合 API 的人的杰作。

如果您想了解更多关于固体设计原则的知识，这里有一些有用的资源供您参考:

1.  [干净的代码](http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882?tag=javamysqlanta-20)作者【罗伯特·马丁】
2.  [面向对象设计的坚实原则](https://pluralsight.pxf.io/c/1193463/424552/7490?u=https%3A%2F%2Fwww.pluralsight.com%2Fcourses%2Fprinciples-oo-design)
3.  [面向对象设计和架构的坚实原则](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fsolid-principles-object-oriented-design-architecture%2F)
4.  马丁·福勒的《重构》

如果你喜欢这篇文章， 您可能也喜欢这些 **Java 和编程文章**:
[Java 程序员应该学习的 10 件事](https://javarevisited.blogspot.com/2017/12/10-things-java-programmers-should-learn.html#axzz5atl0BngO)
[每个程序员都必须阅读的 10 本书](http://www.java67.com/2015/03/10-books-every-programmer-and-software-engineer-read.html)
[提高编程技能的 10 个技巧](http://javarevisited.blogspot.sg/2014/01/10-tips-to-improve-programming-skill-become-better-programmer.html#axzz553pz1hYh)
[每个软件开发人员都应该知道的 10 个工具](http://javarevisited.blogspot.sg/2018/01/10-tools-every-software-developer-know.html#axzz559dyoLSA)
[深入学习软件架构的 5 门课程](https://javarevisited.blogspot.com/2019/03/5-courses-programmers-can-join-to-learn.html#axzz5jKqbGRcg)
[Java 程序员应该知道的 20 个库和 API](https://javarevisited.blogspot.com/2018/01/top-20-libraries-and-apis-for-java-programmers.html)
[前 10 名](http://www.java67.com/2017/12/10-programming-languages-to-learn-in.html)
[10 框架与库 Java 和 Web 开发者应该学习的](http://javarevisited.blogspot.sg/2018/01/10-frameworks-java-and-web-developers-should-learn.html)
[10 门最好的 Python 课程学习编码](https://medium.com/better-programming/top-5-courses-to-learn-python-in-2018-best-of-lot-26644a99e7ec)

感谢阅读这篇文章。如果你发现这些面向对象的设计原则很有用，那么请与你的朋友和同事分享。如果您有任何问题或反馈，请留言。

> 顺便说一句，如果你使用本文中的链接购买我推荐的任何书籍或课程，我也会得到报酬。

如果你喜欢这篇文章，那么请考虑关注我。如果你想得到每一个新帖子的通知，别忘了在 Twitter 上关注[**javarevited**](https://twitter.com/javarevisited)！

* * *

> 如果你真的对你的编码充满热情，并且想要提高你的编码技能，没有比这本书更好的了，那就是罗伯特·马丁的**[和马丁·p·福勒的](https://medium.com/u/ffa32c7b39eb)[重构](http://www.amazon.com/Refactoring-Improving-Design-Existing-Code/dp/0201485672?tag=javamysqlanta-20)。去看看吧。*****