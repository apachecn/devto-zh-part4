# 语句和表达式的区别

> 原文：<https://dev.to/renegadecoder94/the-difference-between-statements-and-expressions-1e47>

随着我对编程语言和一般语言越来越感兴趣，我发现理论并不总是与现实相符。例如，我刚刚了解了语句和表达式之间的区别，以及这种区别在现代编程语言中并不总是显而易见的。

## 背景

作为一名在职博士生和研究生助教，我一直在关注如何成为一名优秀的教授。为此，我一直在向不同的教师学习他们的经验和哲学。最近，我了解了陈述和表达的区别，所以我想和你分享会很有趣。

奇怪的是，我实际上是在接受教授软件基础课程的培训时才知道这种区别的。作为培训的一部分，我必须完成所有的编程作业，这样我才能从老师那里得到反馈。有一次，老师向我提到他们不喜欢下面的 Java 语法:

```
a[++i] 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们有一个通过`++i`访问的数组。换句话说，我们递增`i`,然后在该索引处访问`a`——所有这些都在一行中。看到什么问题了吗？如果没有，也不用担心！这就是今天文章的主题。

## 术语

一开始，我想区分两个术语:表达和陈述。这些术语将构成为什么`a[++i]`被认为是不良实践的论据基础。

### 表情

在计算机科学中，当我们谈论表达式时，我们指的是任何可以被求值以产生值的东西。自然地，我们可以把任何数据本身看作一个表达式，因为数据总是对自己求值:

```
4
"Hi!"
x
'w'
true
9.0 
```

Enter fullscreen mode Exit fullscreen mode

当然，表达式可以由表达式组成:

```
4 + 2
"Hi," + " friend!"
x * y
'w' + 4
true == !false
9.0 / 3 
```

Enter fullscreen mode Exit fullscreen mode

在每一个场景中，我们都使用操作符来嵌套我们的表达式，所以我们得到了类似下面的语言语法:

```
<expr>: number 
      | (<expr>) 
      | <expr> * <expr> 
      | <expr> + <expr> 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们创建了一个愚蠢的语法，它将表达式定义为一个数字、一个括号中的表达式、一个表达式乘以一个表达式，或者一个表达式加上一个表达式。你大概可以想象，表达式有很多种写法。唯一的规则是表达式必须返回值。

### 报表

相反，语句不返回任何内容。相反，它们执行一个动作，这个动作引入了某种形式的状态(也称为副作用)。下面的列表包含一些语句的例子:

```
x = 5
if (y) { ... }
while (true) { ... }
return s 
```

Enter fullscreen mode Exit fullscreen mode

如果我们仔细观察，我们可能会注意到一些语句包含表达式。然而，这些陈述本身并不评估任何东西。

关于语句，有趣的是它们依赖于顺序。为了理解某个陈述，理解导致它的上下文是很重要的。

相反，表达式不依赖于状态，因为它们不会产生副作用，所以任何嵌套的表达式都可以直接推理。例如，请注意我们如何隔离以下表达式的任何部分并评估其结果:

```
((6 * 7) + (5 + 2 + 1)) > 17 
```

Enter fullscreen mode Exit fullscreen mode

当然，任何外部范围都依赖于内部范围的结果，但是评估`(6 * 7)`对`17`没有影响。因此，即使表达式的元素发生了变化，也很容易对其进行推理。欢迎学习函数式编程的基础——但是，这是另一个时代的话题！

## 有什么蹊跷？

不幸的是，虽然我提供的定义非常清晰，但是现代编程语言并不总是遵循相同的原则。比如`++i`是一个语句还是一个表达式？技巧问题:可能两者都有。

在 Java 中，`++i`和`i++`可以作为独立的语句来改变程序的状态。例如，它们通常用于在 For 循环中增加变量。然而，除此之外，它们还可以用作表达:

```
a[++i]
a[i++]
someFunction(i++) 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，`++i`返回一个值，而这个值不同于`i++`。正如您可能想象的那样，语句和表达式之间的这种模糊性会导致一些令人讨厌的错误。例如，您认为下面的程序是做什么的？

```
i = 0
while (i < 5) { 
    print(i) 
    i = i++
} 
```

Enter fullscreen mode Exit fullscreen mode

[在不进入杂草](https://dev.to/renegadecoder94/the-behavior-of-i--i-in-java-ch1)的情况下，这段代码可能会做许多不同的事情。在 Java 中，尽管在第 4 行中明显增加了`i`,它实际上会无限期地打印零。事实证明，后缀`++`操作符在将`i`的值增加 1 后返回它的旧值。换句话说，`i`递增，然后重置为零。

语句和表达式之间的模糊性的后果是巨大的，它也延续到函数和过程中。

## 但是等等，还有更多

通常，像方法、函数、过程和子例程这样的术语都可以互换使用。事实上，你可能会发现我在自己的网站上很难区分这些术语。话虽如此，但函数和过程之间至少有微妙的区别，所以我们来谈谈。

### 功能

像数学函数一样，给定一些输入，编程函数返回一个值:

```
int getLength(String s) { ... }
double computeAreaOfSquare(double length) { ... }
double computePotentialEnergy(double m, double g, double h) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，函数的返回类型不能是 nothing(即 void)。因此，函数类似于表达式:它们返回一个没有任何副作用的值。事实上，他们经常在表达的地方工作:

```
(getLength(s1) * 2) > getLength(s2) 
```

Enter fullscreen mode Exit fullscreen mode

根据定义，函数就是一个表达式。

### 程序

相反，过程不返回值。相反，他们执行一些动作:

```
void scale(Square sq, double sc) { ... }
void insertElementAt(int[] list, int index, int element) { ... }
void mutateString(char[] str) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

因此，过程与语句的关系更为密切，因为它们只会产生副作用。自然，它们不能用作表达式:

```
mutateString(s) * 4 // What? 
```

Enter fullscreen mode Exit fullscreen mode

根据定义，过程就是一个语句。

## 模糊界限

就像表达式和语句一样，现代编程语言模糊了函数和过程之间的界限。在某些情况下，甚至不可能将两者分开。

考虑 Java，它有一个*传值*系统。如果我们要设计一个数据结构，我们经常会实现像`add`、`remove`、`push`、`pop`、`enqueue`、`dequeue`等动作。这些行为是直观的，因为它们按照我们期望的方式工作。例如，如果我们想向堆栈中添加一个元素，我们将调用`push`并将该元素的副本作为输入。

现在，假设我们想要实现一个 remove 方法(即`pop`)。我们如何着手去做而不模糊功能和过程之间的界限？显然，`pop`有一个副作用:它从堆栈中移除了顶部的元素。然而，理想情况下，我们也希望能够返回该值。因为 Java 是[传值的](https://www.mathwarehouse.com/programming/passing-by-value-vs-by-reference-visual-explanation.php)，我们不能通过我们的一个参数将对元素的引用传递回调用者。换句话说，我们一直在创建一个有副作用的函数。

因此，我们的`pop`方法既可以用作表达式，也可以用作语句。当在一个表达式中使用时，突然很难推断这个表达式在做什么，因为表达式的各个部分可能会看到堆栈的不同状态。此外，对同一表达式的连续调用可能会产生不同的结果，因为堆栈的状态在每次调用时都会发生变化。

也就是说，有一种方法可以解决这个问题。我们可以创建一对方法，一个函数和一个过程，从堆栈中获取顶层元素(`peek`)并移除该元素(`pop`)。这里的想法是我们保持[纯函数](https://alvinalexander.com/scala/fp-book/definition-of-pure-function)和过程之间的分离。换句话说，我们可以在不修改堆栈的情况下，使用`peek`来知道堆栈顶部的值。然后，我们可以使用`pop`删除顶部元素。

当然，引入一个纯函数和一个过程来代替一个有副作用的函数需要一点训练，这可能会有回报，也可能不会有回报。是否值得努力，由你自己决定。

## 讨论

对我来说，了解语句和表达式之间的区别引发了一系列关于语言设计的问题。毕竟，世界上有数百万人在编码时没有考虑到这些细节，所以我的问题是:这真的重要吗？

最近，我注意到了函数式编程(FP)的趋势，我想知道这是否是所有技术债务的结果，这些债务是由表达式和语句之间模糊的界限建立起来的。如果不是，这种向 FP 发展的趋势真的只是炒作吗？毕竟，FP 并不新鲜。例如，Lisp 已经有 60 多年的历史了，这在技术社区中已经存在很久了。你有什么想法？

当你在这里的时候，看看这些相关的文章:

*   [Lisp 中的 Hello World](https://therenegadecoder.com/code/hello-world-in-lisp/)
*   [Java 中‘I = i++’的行为](https://dev.to/renegadecoder94/the-behavior-of-i--i-in-java-ch1)
*   [使用模运算的石头剪刀布](https://dev.to/renegadecoder94/rock-paper-scissors-using-modular-arithmetic-2j4p)

另外，如果你对网站的发展感兴趣，我有一个[邮件列表](http://eepurl.com/dB-ABr)，在那里你会收到关于新文章的每周邮件。或者，你可以[成为正式会员](https://therenegadecoder.com/members/)，这样你就可以访问博客了。无论如何，感谢你花时间阅读我的作品！

声明和表达的区别这个帖子首先出现在变节的程序员 T2 的文章中。