# If -过程化、功能化、面向对象

> 原文：<https://dev.to/stereobooster/if-procedural-functional-object-oriented-a4d>

先说一下`if`的说法。许多人认为它是编程的基石，例如，每当我讨论 CSS 是否是编程语言的问题时，有人会说:“CSS 没有`if`语句，所以它不能被认为是 PL”。我不知道这个想法从何而来。`if`语句是众多控制流结构中的一种，像 goto、jump、exceptions、loops 等。

## 程序化

我猜`if`最广为人知的形式是它的“程序化”或[结构化](https://pdfs.semanticscholar.org/013b/f90f472e49c05263b90d9e36f8d2705e7fc7.pdf)形式:

```
if (condition) {
  thenAction();
} else {
  elseAction();
} 
```

我说的“过程化”指的是命令式编程的类型，但比冯·诺依曼机器更有组织性。(命令式编程是当你直接向机器发出指令，执行步骤 1、步骤 2 等。)

我将给出例子来代替精确的定义:

*   最命令式语言:汇编(机器码)，Fortran。
*   不太命令式的语言，所谓的“过程化”或[结构化](https://pdfs.semanticscholar.org/013b/f90f472e49c05263b90d9e36f8d2705e7fc7.pdf) ): Algol，Pascal(我猜也是 Go)。

## 功能性

为了用函数式风格实现`if`，我们只需要将自己限制在函数上:

```
// Implementation:
const True = (x) => (y) => x;
const False = (x) => (y) => y;
const If = (condition, thenAction, elseAction) => {
  const action = condition(thenAction)(elseAction);
  return action();
}
// Usage:
If(condition, thenAction, elseAction); 
```

这个想法是对 Church 编码`True`，`False`的直接翻译，在 lambda 演算中:

```
True = λx.λy.x
False = λx.λy.y 
```

## 面向对象

为了在函数式风格中实现`if`,我们只需要将自己限制在对象上:

```
// Implementation:
class TrueClass {
  Then(callBack) {
    callBack.call()
    return this
  }
  Else(callBack) {
    return this
  }
}
class FalseClass {
  Then(callBack){
    return this
  }
  Else(callBack){
    callBack.call()
    return this
  }
}
const True = new TrueClass();
const False = new FalseClass();
// Usage:
// const condition = True or False
condition.Then(thenAction).Else(elseAction) 
```

## 结论

在实践中，许多 OOP 和 [FP 语言](https://wiki.haskell.org/If-then-else)使用过程式`if`(你可以不用它)。

我给出这些例子的原因是为了展示“纯粹的”FP 和 OOP 实现，以便您可以比较它们。为了了解全貌，我需要提到 OOP 示例是 SmallTalk 风格，SmallTalk 从 Scheme 中获得了一些灵感，而 Scheme 又使用了 lambda calculus `¯\_(ツ)_/¯`中的思想。有些人会认为“纯”OOP 的定义是闲聊，让我们把它留给另一篇文章。

> 照片由汤姆·帕森斯在 Unsplash 上拍摄