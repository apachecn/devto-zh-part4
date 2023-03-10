# 接口隔离(ISP)

> 原文：<https://dev.to/mangelsnc/segregacion-de-interfaces-isp-437p>

[![No todo debe de ir a parar al mismo cubo](img/1375bec96c40bd037151163d8f184c5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ok4wClpa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4096/1%2AzLFtG2_FEA0NIfnGNam5aA.jpeg)

今天你们所关注的固体原理就是所谓的界面隔离原理，从现在起 ISP。

## 说明

ISP 由 [Bob C 表示。Martin](https://en.wikipedia.org/wiki/Robert_Cecil_Martin) 在施乐公司工作时，他们正在计划一种新的打印机型号，除了打印、装订和传真之外。在为这台打印机开发软件的过程中，Martin 意识到某个特定的类(Job 类)由于方法过多而“T2”过大，因为几乎所有的任务(打印、装订、发送……)都在使用它。这种设计最糟糕的是，每个任务都可以访问它根本不需要的方法(例如，grapar 任务可以访问它将使用的打印方法)。

Martin 的解决方案为每种类型的作业定义了一系列接口:StapleJobInterface、PrintJobInterface 和 FaxJobInterface。Job 类将实现所有这些接口，但 Print、staple 和 Fax 类的方法会将只实现适合其工作的接口的对象作为参数传递给它们，而其它方法则隐藏在服务中。

另一种解释方法是创建接口、从 Job 类中删除方法，以及创建扩展作业并实现每个接口的新类，从而创建 PrintJob、FaxJob 和 StapleJob 对象:

我认为，这种解决办法更符合其他健全的原则，特别是与 [SRP](https://medium.com/all-you-need-is-clean-code/principio-de-%C3%BAnica-responsabilidad-srp-c67c3626ff47) 相一致，因为它将责任划分在不同的类别中。

有了这些背景，我们能定义 ISP 吗？维基百科做得很好：

> # **The principle of interface isolation** (…) stipulates that customers of a given program should only know the methods they actually use, but should not know the methods that they don't need to use.

这意味着我们的接口很小，适合于他们希望给实现它们的类带来的角色，因此也称为角色接口。

众所周知，接口定义行为，但不定义其实现。使用组合机制时，我们可以在类中实现所需数量的接口，而不是仅允许我们继承单个类的继承机制，这就是为什么组合为我们提供了额外的灵活性:能够为同一功能实现不同的实现。

## 我们检测到不符合 ISP 要求吗？

检测不符合 ISP 标准的最简单方法是查看我们的接口及其大小。

*   过多的方法，其中有些方法在一个新的界面中将它们分开会有意义吗？

*   每当一个新类实现接口时，就很难不实施方法，或者抛出异常？

*   你想用界面建模的特征，能分成几个吗？

*   一个用于不同任务的类，这些任务是否可以访问该类中的其它方法？

如果您回答了以上任何一个问题，则可能是 ISP 违例。

## 我能符合 ISP 吗？

**如果你还没有违反**的话，继续一如既往，不要让你的界面变得巨大，用小界面模拟角色，并确保实施它们的类在这样做时没有问题。

如果您已经检测到一个或多个违规，现在是纠正的时候了:e[【l 模式适配器】](https://sourcemaking.com/design_patterns/adapter)在这些情况下，使用转换适配器将一个巨大的接口转换为较小的接口可能会有很大帮助。

下次见！