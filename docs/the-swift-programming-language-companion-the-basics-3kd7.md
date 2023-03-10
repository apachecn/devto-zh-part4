# Swift 编程语言指南:基础

> 原文：<https://dev.to/loufranco/the-swift-programming-language-companion-the-basics-3kd7>

本文是通过编写来自 Apple 的 Swift 编程语言书籍的代码来学习 Swift 的系列文章的一部分。

### Swift 编程语言伴侣

*   [简介](https://dev.to/loufranco/the-swift-programming-language-companion-introduction-4k0a)

### 基础知识

所以，概括一下，我们把书和 Xcode 并排放在一起。我们要读第一章，[基础知识](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html#)，在操场上写代码。

在你开始之前，如果你有一个应用程序的想法，请记住。如果你不知道，就选一些你使用和熟悉的应用程序，并牢记在心。例如，我目前正在开发一款健身手表应用程序，我将使用它来管理我的跑步。我将向你展示我如何用它来为自己创造练习。

#### 常量和变量

当你阅读这一节时，你将学习如何声明常量和变量。这本书举了几个例子。如果你愿意，可以把它们输入到操场上。

然后，根据你的应用程序想法生成类似的代码。比如对于我的 app:

```
let numberOfLaps = 5
var currentLap = 0

var workoutName: String 
workoutName = "5 x 100m" 
```

Enter fullscreen mode Exit fullscreen mode

介绍的另一个非常重要的事情是，有一种用句子描述代码的方法。上面第一行可以读作:“声明一个名为 numberOfLaps 的常数，将其初始值设为 5”。

所以，作为一个练习，

> 声明一个名为 numberOfMinutesRunning 的变量，并将其初始值设置为 0

然后，

> 将 numberOfMinutesRunning 的值更改为 1

#### 接下来是什么

这一章涵盖了声明和类型，但是从可选的开始变得更加复杂。所以，通读一遍，输入代码，根据你记住的某个应用程序生成更多代码，在下一篇文章中，我会更深入地探讨可选方案。

如果你有任何问题，请在下面的评论中提问。