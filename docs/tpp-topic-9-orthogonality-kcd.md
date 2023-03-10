# TPP 主题 10:正交性

> 原文：<https://dev.to/steadbytes/tpp-topic-9-orthogonality-kcd>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/the-pragmatic-programmer-20th/topic-10-challenges/)
> 
> 见实用程序员 20 周年纪念版系列[第一帖](https://dev.to/steadbytes/the-pragmatic-programmer-20th-anniversary-edition-series-1e2l)介绍。

## 挑战 1

> 考虑具有图形用户界面的工具和在 shell 提示符下使用的小型但可组合的命令行实用程序之间的区别。哪一组更正交，为什么？哪一种更容易用于预期目的？哪一套更容易与其他工具结合来应对新的挑战？哪一套比较好学？

命令行实用程序比 GUI 更加正交。命令行实用程序是相互独立的——独立的程序，有独立的代码库和开发人员。它们可以独立更新或完全替换，而不会影响到其他组件。

总的来说，命令行实用程序更容易被用户准确地用于他们想要的目的。这是因为(通常)这就是程序所做的全部*，仅此而已——因此除了预期的目的，它很难用于任何事情*。**

 *命令行实用程序更容易与其他工具结合来应对新的挑战。这些程序通常使用[标准流](https://en.wikipedia.org/wiki/Standard_streams)进行 I/O，允许它们通过[重定向](https://en.wikipedia.org/wiki/Redirection_(computing)#Piping)来完成更大的任务。另一方面，图形用户界面*几乎不可能*组合，因为它们不提供标准的 I/O 方法——例如，人们不能轻易地将一个图形用户界面中的文本框输出到另一个图形用户界面中的文本框。

## 挑战 2

> C++支持多重继承，Java 允许一个类实现多个接口。Ruby 有 mixins。使用这些工具对正交性有什么影响？使用多重继承和多重接口在影响上有区别吗？使用委托和使用继承有区别吗？

这些设施提高了正交性。例如，多重继承允许公共功能在多个较小的类中实现，并添加到其他类中，而无需在该类中重新实现。在继承的类中所做的改变将会出现在从它继承的任何类中- *潜在地*消除了改变多个类的需要。

多重继承允许共享*实现*——执行某些动作的实际代码(当然可以被覆盖)。多个接口允许“共享”*规范*——一个类在没有实际实现的情况下必须遵守的预期 API。当然还有更多细节和细微差别，但我认为这些是要点。

委托允许特定功能的*实现*保留在一个专门用于该功能的类中，同时允许另一个类使用或公开该功能。这通过避免向单个类添加额外的行为来保持正交性。

## 练习 1

> 你被要求一次读一行文件。对于每一行，你必须把它分成几个字段。以下哪组伪类定义可能更正交？

```
class Split1 {
    constructor(fileName) # opens the file for reading
    def readNextLine() # moves to the next line
    def getField(n) # returns the nth field in the current line
} 
```

或者

```
class Split2 {
    constructor(line) # splits a line
    def getField(n) # returns nth field in current line
} 
```

`Split2`更加正交——只有*实现了将一行分割成多个字段的行为，而不关心从某个源(如文件)获取行。这允许在不需要改变字段拆分类的情况下改变行的源，并且允许它在其他上下文中使用。例如，可以重用该类来拆分通过网络从日志聚合 API(即 [AWS CloudWatch `GetLogEvents`](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetLogEvents.html) )流出的日志行，或者拆分通过 HTTP 请求获取的网页行。*

## 练习 2

> 面向对象语言和函数式语言在正交性上有什么区别？这些差异是语言本身固有的，还是只是人们使用语言的方式不同？

一般来说，正交性的敌人是软件系统中本应独立的模块/部分之间的高耦合。面向对象和函数式语言都提供了增加和减少这种差异的方法，这取决于它们的使用方式。

函数式编程倾向于使用大量小的、通常是纯粹的、独立的函数，并将它们组合起来以构建更大的功能模块。这降低了耦合并提高了正交性，因为每个函数都是独立的，并且理论上可以重复使用和改变，而不会影响较大的模块。然而，这些函数对数据结构进行操作，转换数据结构并产生结果，这些结果被提供给其他函数。这可能会产生**难以发现的**函数之间的耦合——更改数据可能会导致跨许多函数的更改。

面向对象编程提供了封装功能模块的类，理论上使它们相互独立。然而(如前所述),多重继承、接口、子类化/重写和许多其他语言特性会导致耦合增加和正交性降低——类从父类继承不需要的方法，改变父类方法实现和破坏所有子类等。

总之，我认为这两种范式之间的正交性主要取决于人们使用它们的方式。然而，根据我的经验，函数式语言的使用方式*倾向于*比面向对象更正交。*