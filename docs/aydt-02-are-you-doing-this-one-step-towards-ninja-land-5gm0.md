# AYDT - 02(你这是在干嘛)-迈向忍者之地的一步。

> 原文：<https://dev.to/chintukarthi/aydt-02-are-you-doing-this-one-step-towards-ninja-land-5gm0>

有句著名的尤达名言，

[![yoda quote](img/22d2a65b59805a84c63439d3478738da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fl39J0_x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u0cyi1iv3l2dxwvbgcoe.jpg)

从这句对开发人员的引用中，我会说:“练习一门语言，直到你在用它编码之前不再需要三思而后行。”

在进入本周的 AYDT 之前，有一个温和的提醒。这是“你在做这个吗？”系列，我强烈建议您看看以前的文章，以了解更多信息。快乐编码👨🏻‍💻

上一周链接:
[https://dev . to/chintukarthi/aydt-01-你正在做这一步走向忍者之地-3eb8](https://dev.to/chintukarthi/aydt-01-are-you-doing-this-one-step-towards-ninja-land-3eb8)

这句话让火凤凰🔥让我们从你的内心来看看本周的 AYDT！！

我们还在等什么？让我们开始吧🏄🏻‍♂️.

### **你在做 This❓-的自我解释功能！**

写函数很酷。根据需要，我们可以跨文件甚至全局调用它。但是，有一个条件。我们中的大多数人会在函数的上方或内部为开发人员或任何将来可能会看到它的人写注释。

尽管编写注释是一个好习惯，但是仅仅编写注释并不能让你的函数更具可读性。

让我们来看看下面的函数。

```
# This function performs the addition of two numbers.

function add(a, b) {
  sum = a+b;
  return sum
} 
```

Enter fullscreen mode Exit fullscreen mode

没什么可疑的吗？嗯，🤔让我们来看看另一个函数。
假设“fibonacci”是一个被另一个名为“fib”的函数调用的函数。

```
# This function gets two input a and b and performs addition operation. 
# After Addition the sum is used to find the fibonacci series

function fib(a, b) {
  sum = a+b;
  fibonacci = fibonacci(sum); 
} 
```

Enter fullscreen mode Exit fullscreen mode

找到什么了吗？是的。这段代码烂透了。😖

但是对于初学者来说，这不会敲响任何钟，大多数人会认为它正在做它应该做的事情。没关系。

但是为什么这不是一个好的实践呢？

因为说到编码，我们不仅要看功能性，还要看代码可读性。

这是大多数初学者犯错误的地方。我也是。

在编写函数时，请记住这一点。

"没人有时间去读你一行行晦涩难懂的评论."

那么，为了让你的函数对开发者更友好，你能做些什么呢？
你应该以这样的方式编写一个函数，即使几年后当开发人员看着你的代码，他们也应该不用看注释就能理解它的功能，这就是我们所说的**“自解释函数”**

那么一个不言自明的函数应该是什么样的呢？

它没有一个标准。尽最大努力让函数自己解释，而不是试图在评论中解释。

我可以在下面提供一些不言自明的函数的例子。

**被警告:**
这些例子并不是某种理想的场景。
这里说的功能都是基于 javascript 的。

给你，

1)第一个函数可以这样写:

```
# Performs addition operation.

function addition(variable_1, variable_2) {
  sum_of_variables = variable_1 + variable_2;
  return sum_of_variables;
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能会发现这个新功能看起来比前一个更复杂。但相信我，不是的。仅仅因为单词增加并不意味着代码复杂。

但好的一面是，“现在比以前可读性强多了，不言自明！”

2)第二个函数可以这样写:

```
# Performs addition operation followed by finding fibonacci of the resulted sum.

function addition_with_fibonacci(variable_1, variable_2) {
  sum_of_variables = variable_1 + variable_2;
  fibonacci_series = find_fibonacci_series(sum_of_variables);
  return fibonacci_series;
} 
```

Enter fullscreen mode Exit fullscreen mode

这些是一些例子，说明你的函数是一个不言自明的函数。如果你遇到或者知道它的一个更好的版本，请在评论区告诉我们。毕竟，我是喜欢每天学习新东西的开发人员之一。

**奖励提示:**

在一个函数中，无论何时何地需要命名约定，就用点心大小写代替骆驼大小写，就像我在上面的函数中做的那样。

如果你想更好地了解蛇案和骆驼案及其用例，请等到下周。🤪

这就是本周的总结。🧙周围的人再见🏻‍♂️.

封面图片提供:[https://www.codeninja.com.sg/](https://www.codeninja.com.sg/)