# 克拉苏拉斯·德瓜尔达

> 原文：<https://dev.to/mangelsnc/clausulas-de-guarda-2hfo>

[![](img/c739c3a19f12df41c2d7c514ef82607e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EosIuSpe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A-T_5P8bsMtaB0OQeMr9VZQ.jpeg)

[保管条款](https://refactoring.com/catalog/replaceNestedConditionalWithGuardClauses.html)是一种简单的方法，使我们的代码**更易于阅读**、**更具语义**和**识别水平较低**。

检测这些反应器的一个好方法是始终查找符合执行主代码条件的结构**【if-else】**，如果不满足条件，则抛出错误或异常。

我相信我们见过很多次这样的代码块:

在方法 **setSpeed** 中，我们可以看到 if-else 构造进行检查，如果通过检查，则执行赋值操作。在这种情况下，这是一个非常简单的操作，但可能需要更多的行，并且我们会在“**if**”内有相当大的代码块，而在“**else**内只有一行，这也将位于整个下方，因此很难阅读。

> # This is a typical case, which can be converted into a custody clause. Reactor [*[extract method]*](https://refactoring.com/catalog/extractMethod.html) is used.

为了实现这一点，首先是绕过**if**，否定条件，删除 **else** :

这里我们可以看到，从一开始，

*   我们已从分配中删除了一个身份级别。

*   **else** 已消失，因为它们是不必要的，如果抛出异常，分配将永远不会实现。

尽管如此， **if** 仍为" "，所以我们将把[保留条款](https://refactoring.com/catalog/replaceNestedConditionalWithGuardClauses.html)的反应器应用于我们班的私人方法 **if** :

如您所见，我们已将代码从 **if** 移至私人方法。按照[清洁守则](http://amzn.to/2vpeq24)的原则，我们把私法放在第一个使用它的方法正下方。

最后的结果显然是一个简单得多的代码，更易于阅读，而且标识级别更低。

回头见！