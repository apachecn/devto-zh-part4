# 有效的 Java 星期二！建设者模式！

> 原文：<https://dev.to/kylec32/effective-java-tuesday-the-builder-pattern-2k5f>

现在是有效 Java 回顾系列的第二集了。今天的主题是构建器模式。就像我们上一章一样，这是另一种创造模式。

当我们面对一个有大量可选成员的类时，创建这些对象的最好方法是什么？Effective Java 中详细介绍的三种方法是伸缩构造函数、JavaBean 模式和 builder 模式。让我们浏览一下这些方法，看看前两个方法的不足之处，以及构建器模式如何能够大放异彩。

选项 1:伸缩构造函数
如果你有不止一两个可选参数，首先是我最不喜欢的选项。伸缩构造函数。伸缩构造函数基本上是一组构造函数，它们基本上涵盖了可以用来创建对象的所有可用的参数排列。我们来看一个例子:

```
public Burger(String bunType)
public Burger(String bunType, List<String> condiments)
public Burger(String bunType, List<String> condiments, String meat)
public Burger(String bunType, List<String> condiments, String meat, String temperature) 
```

Enter fullscreen mode Exit fullscreen mode

看看这些构造函数，你就知道它的名字是从哪里来的了。伸缩构造函数遇到了我在本系列的上一篇文章中提到的一个问题。对于同一类型的多个参数，很容易搞不清每个参数是什么。(尽管我最喜欢的 IDE [Intellij](https://www.jetbrains.com/idea/) 给出了给出参数名称的小工具提示)。在上面的例子中，问题可能是不知道第四个或第五个参数是肉。最好不要搞砸了，否则你可能会得到一个半熟的牛肉😕。上面可以看到的另一个尴尬是，如果我只想要一种调味品呢？嗯，我需要用类似`Collection.singletonList("Ketchup")`的东西创建集合(嘿，看，一个[工厂方法](https://dev.to/kylec32/effective-java-tuesday-let-s-consider-static-factory-methods-170p)！).一种方法是使用 varargs，但是在一个函数中只能使用一个 varargs 参数，而且必须在函数的末尾，这样它的使用就受到了限制。这种方法的一个好处是你可以创建一个不可变的对象，这的确是一件好事。如果你只有一个或两个可选参数，它可以工作，但正如我们所看到的，它有一些问题。

选项 2:JavaBean s
JavaBean s 方法非常简单。首先有一个无参数构造函数，然后为每个成员变量创建 setters。大概意思是:

```
Burger burger = new Burger();
burger.setBread("Sour dough");
burger.setMeat("Beef"); 
```

Enter fullscreen mode Exit fullscreen mode

这很简单，但也有问题。对我来说最大的一个问题是，当你构建对象时，它可能处于不一致的状态。有很多不太好的控制。也感觉有点乱，有点啰嗦。这种方法的另一个缺点是缺乏创建不可变对象的能力。有效的 Java 使用了一种方法，在这种方法中，有些人“冻结”他们的对象，但这似乎很容易出错，而且很混乱。如果没有半创建对象的风险，并且不需要不变性，这种方法可以很好地工作，但是我们也应该考虑这一章是关于什么的。

*选项 3:构建器模式*
这就把我们带到了本章要讲的内容，构建器模式。这种模式让我们两全其美。我们获得了伸缩构造函数的安全性，但也像 JavaBeans 方法一样极大地提高了可读性(我甚至认为比 JavaBeans 更好)。在这个模式中，我们在我们想要创建的对象内部创建一个静态类，传统上简单地称为`Builder`。builder 对象的方法允许用户设置对象的值，然后返回 builder 对象。这允许一个干净流畅的 API。一旦所有的调用完成，用户调用一个无参数的`build`方法，此时对象被实际创建。`build`方法允许有一段时间来确定物体处于固态。就我个人而言，当我有构建器模式所需的属性时，我会将它们放在初始的`build`调用中。

这给了我们什么？首先，不变性的选择。虽然不总是利用它是一个属性，我个人认为是有益的。然而，最大的好处是通过流畅的 API，我们有一个更易于维护的代码库。这也使得可选参数更容易处理。如果类的消费者不需要设置属性，他们就不会调用这个方法。我们还可以看到，我们可以调用方法来用列表构造多个属性，而不会遇到上述方法可能遇到的任何 varargs 问题。

其他语言用命名参数和可选参数来处理这个问题。这也是我喜欢科特林的原因之一。但是如果没有这个选项，构建器模式是固定的。一定有更简单的方法，而且确实有。我认为每个 Java 开发人员都应该拥有的一个工具是 Lombok。这个处理器承担了大量编写 Java 的工作，这很棒。他们还有一个 [@Builder](https://projectlombok.org/features/Builder) 注释，这将使我们非常清楚。有些人[认为龙目岛是个坏主意，但我绝对不是其中之一。](https://medium.com/@vgonzalo/dont-use-lombok-672418daa819)

所以你有它。构建器模式。你用这种模式成功过吗？你经历过什么陷阱吗？请在下面的评论中告诉我们。