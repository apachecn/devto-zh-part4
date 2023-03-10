# 何时使用 LET 或 CONST

> 原文：<https://dev.to/matscode/when-to-use-let-or-const-4fag>

你好，

哇，你对我的观点感兴趣。我也想在下面的评论区了解你的想法。

我已经阅读了来自不同编码者的无数行 javascript 代码。我已经看到了`let`和`const`的不同用例，我想说不是常规的。

所以，我对自己说，也许我应该分享我写代码时使用的地址，*我如何选择是使用`let`还是`const`* 。

#### 我该如何选择？

以下是我的建议；

*   仅在易于变异的原始值上使用`let`。

例如

```
let balance = 475 
```

Enter fullscreen mode Exit fullscreen mode

*   很明显，应该用于你想使之不可变的原始值，并且它的标识符应该是大写的。

例如

```
const PI = 3.14 // do you remember this guy from primary math... 
```

Enter fullscreen mode Exit fullscreen mode

*   最后，总是对对象使用`const`。

例如

```
// this? , you don't want to lose the reference to your object, right?
// which also encourage explicit operations on your object properties

const EmptyObj = {} 
```

Enter fullscreen mode Exit fullscreen mode

希望这点我的小意见有帮助。我再次希望在这篇文章的评论区看到你的意见。这可能会让我从不同的角度看问题。

##### *你喜欢读书吗？*