# Swift 编程语言指南:选项

> 原文：<https://dev.to/loufranco/the-swift-programming-language-companion-optionals-1p1h>

本文是通过编写来自 Apple 的 Swift 编程语言书籍的代码来学习 Swift 的系列文章的一部分。

### 《Swift 编程语言指南概述》

[简介](https://dev.to/loufranco/the-swift-programming-language-companion-introduction-4k0a)

我们建立了一个阅读环境，让你边读边写代码。对于 Swift 编程语言，我们正在使用 Xcode 游乐场。

[基础知识](https://dev.to/loufranco/the-swift-programming-language-companion-the-basics-3kd7)

接下来，我们挑选了一些应用程序的想法(在我的例子中是一个用于 Apple Watch 的锻炼应用程序)。我们将使用这个应用程序来将书中的所有代码示例更改为基于我们的应用程序生成的示例。如果你没有一个应用程序的想法，就挑一个你经常使用的应用程序。重要的是，它将帮助我们获得新的代码示例。

另一个重要的教训是，我们学会了如何用文字描述代码。这很重要，因为随着我们的进展，我将使用常规的句子，我认为你应该能够根据我们在书中的位置翻译成代码。

此时，你应该已经阅读了*Swift 编程语言*中的[基础知识](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html#ID330)。你应该为这一章准备一个游戏页面，里面有你在阅读这本书时生成的代码。

### 基础练习题

我们将把所有这些例子建立在一个追踪你吃了什么的应用上。每个练习都是新的代码行。

1.  声明一个名为`appleCalories`的常数，并将其设置为 50
2.  声明一个名为`caloriesEatenToday`的变量，并将其设置为 850
3.  声明一个没有初始值的名为`food`的字符串变量
4.  设置`food`等于`"banana"`
5.  做一个评论，说“这是一份食物清单”
6.  在注释下，声明三个名为`food1`、`food2`和`food3`的常量，并将它们设置为不同食物的名称(字符串)
7.  声明一个名为`bananaCalories`的常数，并将其设置为任意浮点数
8.  声明一个名为`eatenEnoughToday`的变量，并设置为布尔常量(`true`或`false`)
9.  编写一个检查`eatenEnoughToday`的`if-else`语句，并打印“太好了！”如果是`true`，如果是`false`，就“多吃点”。
10.  基于这个应用程序，为我们在这些练习中没有涉及的部分制作一些示例。尤其是你没看懂他们的时候(或者在评论里找我帮忙)。

如果你愿意的话，给我发你的操场页面的信息来寻求反馈。

### 选项

所以，我想单独谈谈可选方案，但与其解释它们，我更希望这些帖子是一个真正的“伴侣”，并浏览这本书本身的文本。

> 在可能缺少值的情况下，可以使用选项。可选表示两种可能性:要么有一个值，您可以打开可选来访问该值，要么根本没有值。

因此，`Bool`代表`true`或`false`，但是可选类型`Bool?`的变量也可以是`nil`，这意味着该变量根本没有值。

该示例给出了一个将`String`值转换为`Int`的函数。如果字符串值为“14”，那么该函数可能返回 14。但是，如果字符串是“Hello ”,它应该做什么。通过使用`Int?`，它可以在这种情况下返回`nil`。

#### 避免`!`运算符用于任何实数代码

下一节将介绍可选的`!`操作符。当把它附加到变量上时，它“强制展开”。所以，如果它不为零，很好，它会得到值。但是，如果它为零，你的应用程序就会崩溃。在操场上用是没问题的，但是到了真正的代码，就要避免，直到你真正理解了如何安全地使用它(是的，有安全的时候)。类似地，避免将它附加到类型注释来创建“隐式展开”的变量。

许多 Swift 开发人员将这些操作符称为“崩溃操作符”。他们最初的名字“爆炸算子”也同样不吉利。

#### 相反，理解`if-let`、`guard-let`和安全解包运算符(`?`、`??`)

这是书中的`if-let`例子:

> 您可以重写 Optionals 部分中的 possibleNumber 示例，以使用可选绑定而不是强制展开:
> 
> ```
> if let actualNumber = Int(possibleNumber) {
>     print("The string \"\(possibleNumber)\" has an integer value of \(actualNumber)")
> } else {
>     print("The string \"\(possibleNumber)\" could not be converted to an integer")
> } 
> ```

在操场上玩玩这段代码，在这段代码前创建一个名为`possibleNumber`的变量，并将其设置为不同的字符串(如“123”、“hello”等)

### 接下来是什么？

接下来，我们将继续讨论[基本运算符](https://docs.swift.org/swift-book/LanguageGuide/BasicOperators.html)。现在阅读它(并编写代码),我将在下一篇文章中介绍它。