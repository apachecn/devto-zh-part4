# 为什么 switch 比 if-else 更好

> 原文：<https://dev.to/mortoray/why-switch-is-better-than-if-else-9cj>

在 Ben 的文章中，他质疑 switch 语句是否比 if-else 链更简洁。我认为它们是，因为它们更好地表达了代码的语义，减少了出错的空间，减少了重复，并潜在地提高了性能。

[![ben image](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 我从来没有过分相信 switch 语句比‘if else if else if else if else’更简洁

### 本哈尔彭 6 月 28 日 191 分钟阅读

#healthydebate #codequality](/ben/i-ve-never-become-overly-convinced-that-switch-statements-are-that-much-cleaner-than-if-else-if-else-if-else-if-else-81l)

# 更好的语义

Switch 语句表达的含义与一系列 if-else 语句不同。开关表示您正在从一个输入值映射到一段代码。

```
switch( expr ) {
    case value_0:
        ...

    case value_1:
        ...
} 
```

Enter fullscreen mode Exit fullscreen mode

显而易见，我们试图涵盖`expr`所有可能的值。阅读这段代码的人可以很快确定应该发生什么。

这对于等价的 if-else 链来说就不那么清楚了:

```
if( expr == value_0) {
    ...
} else if( expr == value_1) {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里不能确定我们是打算覆盖所有可能的值，还是只覆盖这些特定的值。

许多编译器会在`switch`语句缺少条件时通知您。在 C++中，这可能是枚举中缺少的`case`语句。在 Rust 中，等价的`match`构造覆盖了更大范围的输入，并且不允许遗漏覆盖。编译器的这种自动检查可以防止常见的缺陷。例如，如果你给一个枚举添加了一个新值，编译器会告诉你所有你没有覆盖新情况的位置。

# 少了错误和废话的余地

if-else 链的一个问题是它允许对任何变量进行任何比较。对表单没有限制增加了隐藏错误的能力。

```
if( expr == value_0 ) {
    ...
} else if( expr == value_1 ) {
    ...
} else if( expr2 == value_2 ) {
    ...
} else if( value_3 == expr ) {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

为什么里面有个`expr2`？现在还不清楚代码是否打算覆盖`expr`的所有值，或者条件只是巧合地相似。

`value_3 == expr`的颠倒顺序呢？这应该在代码审查中得到纠正，但这是造成混乱的另一种可能性。

重构会产生这种问题。个人可以修改表达式，或者修正错误，或者清除错误。在一个并行分支中，另一个程序员添加了一个新的表达式。在合并过程中，两种不同的代码形式会结合在一起，导致不一致的形式。

# 减少重复

if-else 的长链有不必要的语法开销。冗余是源代码的主要弊端之一。从前面的`expr2`的例子中，我们看到了一个表达式的重复输入是不可忽略的。你不能看一眼 if-else 链就认为它像开关一样工作，因为它可能不是。冗余增加了阅读代码时的认知负荷。

这种重复可能会对性能产生影响，并为错误创造了另一个途径。到目前为止我只使用了`expr`，但是如果这个表达式是一个函数调用呢？

```
if( next_obj().get_status() == state_ready ) {
    ...
} else if( next_obj().get_status() == state_pending ) {
    ...
} else {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这里第一个潜在的错误是对`next_obj`的调用。如果第一个条件为真，它将评估一次。如果第一个条件为假，下一个 If 语句将再次调用该函数。它每次都返回相同的值，还是在列表中递增？

那`get_status()`呢？调用这个函数是便宜还是昂贵？也许它会慢慢计算或调用数据库调用？无论代价如何，调用它两次都会加倍，这在许多情况下可能很重要。

为了避免这两个问题，将这些值存储在临时中是很重要的。不幸的是，这是许多编码人员忘记做的事情，因为他们快速复制粘贴第一个 if-else，然后重复。

```
state = next_obj().get_status()
if( state == state_ready ) {
    ...
} else if( state == state_pending ) {
    ...
} else {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`switch`语句来避免这个问题，该语句只对表达式求值一次。

# 性能更佳

在许多情况下，switch 语句比 if-else 链执行得更好。严格的结构使得优化器很容易减少比较的次数。

这是通过创建潜在选项的二叉树来完成的。如果您的 switch 语句包含八种情况，只需要三次比较就可以找到正确的情况。

```
switch( c ) {
    case 0: ...
    case 1: ...
    case 2: ...
    case 3: ...
    case 4: ...
    case 5: ...
    case 6: ...
    case 7: ...
} 
```

Enter fullscreen mode Exit fullscreen mode

一个优化的编译器，或者智能运行时，可以将这个问题简化为数字的二分搜索法。

```
if( c <4 ) {
    if( c < 2 ) {
        if( c == 0 ) {
            //0
        } else {
            //1
        }
    } else {
        if( c == 3 ) {
            // 3
        } else {
            // 4
        }
    }
} else {
    //repeated for 4...7
} 
```

Enter fullscreen mode Exit fullscreen mode

一个聪明的优化器*可能会以同样的方式识别 if-else 序列。但是语句中潜在的微小变化降低了这种可能性。例如，函数调用、隐藏赋值或使用替代变量都会阻止这种优化。*

通过使用语义上有意义的高级形式，您为优化器提供了更多的选项来改进您的代码。

# 语言问题

然而，Switch 语句并不是没有问题。特别是需要显式`break`语句的 C 和 C++形式是有问题的。不过，它也允许多个案件包装在一起。

我非常喜欢 Python，尽管我对它没有 switch 语句感到不安。虽然地图和功能分派涵盖了几种情况，但并没有涵盖所有情况。

Rust 有一个更好的说法。它保留了 switch 语句的高级语义，但是增加了更好的模式匹配。虽然我不喜欢这种语言，但我认为它是 switch 语句的最佳版本。我应该称之为模式匹配，在语言设计中，这是对这些特性的更一般的称呼。你也会在其他语言中看到，比如 Haskell。

也许这就是`switch`最大的问题。感觉像是适当模式匹配的发育不良版本。但这并不是完全抛弃它并回到 if-else 的理由。Switch 语句表达语义，避免重复，减少出错几率，从而产生更简洁的代码。