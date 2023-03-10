# YDKJS —范围和闭合—第 5 部分

> 原文：<https://dev.to/nabendu82/ydkjs-scopes-and-closures-part5-554p>

欢迎来到 YDKJS 系列的第五部分。正如在第 1 部分中所讲的，这个系列是基于我从传奇系列书籍 Kyle Simpson 的《你不知道的 JS》和 Javabrains 的 Kaushik Kothagal 的《Javascript》系列中学到的。

我们将在这里开始 Javascript 最复杂的话题之一，即**闭包**。但是有一点要知道，在 JS 中*闭包无处不在。*

让我们考虑一个没有闭包的简单例子，稍后我们将重构它以使用闭包。

[![Simple inner outer example](img/47b6e06b2afc337db17dab9c2ea8190c.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--acRiSQQW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2568/1%2A9aROQb1AXISx_xeCieUymg.png) *简单的内外层例子*

以上是在外部()中使用内部()的简单示例。inner()是在外部函数中声明和执行的。

现在，因为在 JS 函数是一等公民。我们也可以在任何地方执行函数 inner。我们将使用函数表达式来重构上面的代码。

[![Refactoring inner outer example](img/ee818eac890767e4d5220122a5921734.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Z5K_PwfM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2574/1%2AYcxoQRikzlCKI2_eJBr6Bw.png) *重构内在外在示例*

所以，我们把 inner 改成了函数表达式并返回。因此，当我们调用 outer 时，我们可以将返回值保存到变量 **innerFn** 中。

让我们明白一件事。变量 b 在外部函数 ie 中有一个作用域，从第 4 行到第 10 行。所以，在第 13 行当我们调用外部函数时，它可以访问 b 的值，但是在第 14 行没有外部函数。

那么，innerFn()如何访问 b 的值呢？这就是闭包的 JS 特性发挥作用的地方。当在第 6 行创建 var inner 时，JS 引擎不仅存储函数对象信息，还存储它的作用域信息。因此，它在内部函数对象中存储了变量 a 和 b 的范围。现在你在哪里调用 inner 并不重要，不管是在这个文件中还是在某个第三方文件中。它会一直记住 a 和 b 的值，就像拍了快照一样。

需要注意的是，快照是指向这些变量的指针，而不是副本。所以，如果你在这里改变变量 a 和 b，它将在函数内部被改变。

还有一件事要知道，如果我们调用 outer 2 次，那么 a 和 b 的拷贝数是多少。

[![Copies of a and b](img/97c2227697779facacf31d5fa719d049.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--o7cf7yJm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2870/1%2AWyfSnx57dF48YvB9s762IA.png) *副本 a 和副本 b*

如上图所示，在内层添加一行来增加变量 **a** 和 **b** 。因为 a 是全局变量，所以第一次它的值是 11，第二次是 12，然后调用 innerFn2。

但是 b 在内部函数中有一个快照，所以通过一个新的 innerFn 的每个新调用将从 20 开始 b，并由于增量而将其打印为 21。

现在让我们看一个闭包的实际例子。Javascript 是单线程的，所以如果我们想让问题等等，我们可以使用 JS 提供的 setTimeout 函数。

[![closures in action](img/ee4cac31f6a2c18b43ae5f12f0944723.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--8CDeLc4l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AvSimsmfOrzmt2LK_NKHbUA.png) *封锁线在行动*

这里第 7 行的 setTimeout 调用函数 fn。此 fn 是一个函数表达式控制台日志变量 a。现在，当创建此 fn 时，它会获得变量 a 的快照，即使从另一个没有变量 a 概念的文件中调用 setTimeout，此变量也会在 6 秒钟的延迟后打印出来。

现在，让我们考虑闭包的另一个实际用途。在像 Java 这样的面向对象语言中，我们有 getter 和 setter 的概念。通过这种方式，我们在函数/类中隐藏了外部世界的变量，并使它们成为私有的。它们只能通过 getter 方法访问，通过 setter 方法设置。

将尝试用 Javascript 创建它们。考虑我们下面的第一次尝试。

[![First try for getters](img/1ce27c991653ccd32a3078913533285c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DlGha36V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2878/1%2AN7jutkyH_2OUBNNGLDhPeQ.png)

这里我们创建了一个 person 对象，并有两个保存值的属性:名为**的属性**和姓为**的属性**。然后两个 getter**get first name**和 **getLastname** 来获取这些值。
正如我们在控制台中看到的，我们可以执行 **person.getFirstName()** 但是我们也可以执行`person.firstName`
我们不应该这样做，但是我们没有像 Java 那样在 Javascript 中使用私有变量的概念。

因此，我们将重构我们的代码，使用闭包的概念来达到同样的目的。考虑下面的代码。

[![getters in Javascript](img/3dc1d62d4962556ee1538380e2ac2e6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IPFrc7al--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2874/1%2AgtLOjEHiHphYLaFF0Vqw4g.png)*Javascript 中的 getters】*

我们首先将对象放在函数 **createPerson** 中。然后我们将变量**的名字**和**的姓氏**移出对象。我们在对象内部有通常的 **getFirstName** 和 **getLastname** 。然后我们返回这个对象。

现在在第 16 行，我们调用函数，然后将返回值赋值给变量 person 中的对象。
person 变量可以访问**名**，但不能访问**名**。因为**名字**范围是从第 2 行到第 13 行，在此之外无法访问。

但是 **person.getFirstName()** 是一个执行的闭包。当在第 6 行创建 **getFirstName** 时，由于闭包的概念，它获得了变量 **firstName** 的快照。这就是 **person.getFirstName( )** 能够访问 **firstName 的值的方式。**

我们的“您不知道的 JS——作用域和闭包”系列到此结束。不久将在对象和原型系列中与您见面。