# 语法:赋值

> 原文：<https://dev.to/awwsmm/bits-of-syntax-assignment-2f5o>

最近，我对编程语言设计非常感兴趣。一种语言的语法会影响它的可读性和可用性，但它也会影响它的美观，并影响开发人员在使用它时的想法。

Java 凭借其强大的面向对象设计，可以让程序员专注于数据层次、变量范围以及代码库不同功能组件之间的关系。像 Haskell、R 和 Scala 这样的强函数语言更倾向于考虑数据流、lambda 表达式和 map-reduce 管道。像 C 和汇编这样的“裸机”语言可以让你从代码中挤出尽可能多的性能，同时希望不要试图访问越界内存。

我对软件工程师和语言设计师在过去 80 年左右的时间里是如何用数百种(如果不是数千种)不同的方法来解决弥合二进制语言和人类可读语言之间的鸿沟的问题非常感兴趣。这就是我开始这一系列讨论的原因:向每个人请教你喜欢什么样的语法，讨厌什么样的语法，以及你可以不在乎什么样的语法。我告诉你我的想法，你告诉我你的想法！

* * *

在这次就职讨论中，我想重点谈谈 [**赋值**](https://en.wikipedia.org/wiki/Assignment_(computer_science)) 。有许多不同的方法来声明和初始化变量。变量赋值可能是所有基本编程概念中操作符种类最多的；我们得到了标准:

```
x = 5 
```

Enter fullscreen mode Exit fullscreen mode

更明确的

```
x := 5 
```

Enter fullscreen mode Exit fullscreen mode

我最喜欢的，我认为它给人一种强烈的运动感，并且最接近数学

```
x  <-  5 
```

Enter fullscreen mode Exit fullscreen mode

有更多的语言使用像`let`和`set`这样的关键字来声明变量，还有 LISP，它当然使用了很多括号:

```
(let ((x 12)) ...) 
```

Enter fullscreen mode Exit fullscreen mode

你最喜欢的赋值运算符是什么？你会如何解决分配/平等的困境？还有你怎么看待多重/平行作业？(例如，我喜欢一些语言使用模式匹配在同一行代码中给多个变量赋予不同的值，比如:`(a, b) = (3, 4)`。)

如果让我选择，我会说我更喜欢*向前*的任务，比如

```
4  ->  x 
```

Enter fullscreen mode Exit fullscreen mode

我知道也许从中学代数开始，它就被灌输到我们的头脑中，但我不明白为什么变量赋值从右向左(RTL；`x <- 3`)而不是从左到右(LTR`3 -> x`)。基本上我们剩下的所有代码(以及[世界上大多数语言](https://www.w3.org/International/questions/qa-scripts.en#directions))都是用 LTR 编写和读取的。

像这样做赋值也使得流水线操作更容易。许多语言允许你通过使用管道操作符跳过函数的第一个参数。所以与其:

```
sqrt(3) 
```

Enter fullscreen mode Exit fullscreen mode

...你可能会做类似
的事情

```
3 | sqrt() 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
3  %>%  sqrt() 
```

Enter fullscreen mode Exit fullscreen mode

LTR 赋值会使代码*很好地流动*。而不是:

```
x = pow(sqrt(log(5)), 2) 
```

Enter fullscreen mode Exit fullscreen mode

...我们可以做

```
5  ->  log()  ->  sqrt()  ->  pow(2)  ->  x 
```

Enter fullscreen mode Exit fullscreen mode

...它读起来像一系列的步骤:“取 5，并得到它的对数，然后取结果并得到平方根，然后……”

如果我写自己的语言，我会强制执行 LTR 赋值，并添加一些[语法糖](https://en.wikipedia.org/wiki/Syntactic_sugar)以允许那些空括号也被删除，给出类似于
的东西

```
5  ->  log  ->  sqrt  ->  pow(2)  ->  x 
```

Enter fullscreen mode Exit fullscreen mode

你怎么想呢?你喜欢它还是讨厌它？

很期待听听大家的想法！