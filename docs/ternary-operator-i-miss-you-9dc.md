# 三元算子，我想你

> 原文：<https://dev.to/hooloovoochimico/ternary-operator-i-miss-you-9dc>

Kotlin 是一种很棒的语言，但我记得当我看到它没有三元运算符时，我真的很难过。我是一名 Java 开发人员，我经常使用这个操作符，因为我认为它写起来很快，读起来也很容易。

等等，等等，是的:我知道`if`表达式返回值，但是我觉得在主体只有一条指令的情况下，写一个`if`是多余的。

例如:

我宁愿写这段代码

```
val foo = booleanValue ? something : anythingElse 
```

Enter fullscreen mode Exit fullscreen mode

比这个

```
val foo = if (booleanValue) something else anythingElse 
```

Enter fullscreen mode Exit fullscreen mode

此外，由于主体可能表示复杂的表达式，使用三元运算符，我可以编写更少的代码行。

是的，你说得对，我懒；但如果我不是那么懒，我会去当农民而不是开发商。

是的，当代码过于简洁时，你是对的，可读性较差，我也相信可读性和简洁性之间的平衡是一件好事，但我不认为三元运算符是问题所在。

事实上，Kotlin 的拥护者建议我们避免使用`if`列表，因为它会导致许多错误，那么为什么不应该保留三元运算符呢？

你怎么想？你同意我的观点吗？