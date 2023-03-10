# 原因是什么？

> 原文：<https://dev.to/itminds/what-is-the-reason-4ho4>

## 为什么是新语言？

编译语言有很多好处。其中一个能够控制输出。

ReasonML 不是它自己的东西，更多的是 OCaML 上面的一个语法。众所周知，React 创建 ReasonML 是为了编写更强大、类型更安全的 web 应用程序。当考虑 React(不变性，Props)的概念时，它实际上不太适合作为一种语言的 JavaScript。

已经有一些措施来缓解这种情况，比如 proptypes 和不可变 JS，但这更多的是对症状的治疗，而不是潜在的疾病。

现在，不要误解我，我喜欢 JavaScript。我喜欢利用 JavaScript 的技术，但是当 React 试图戴上 JavaScript 的帽子时，感觉更像是给 Orca 穿上鞋子(我认为这可以做到，但看起来很傻，需要大量的胶带)。

愤怒的 JavaScript 开发者说:“我不想学一门新语言。”。

但是，如果我告诉你，ReasonML 看起来很像 JavaScript，但却能让你写出真正类型安全的代码，那会怎么样呢

让我们用一个简单的加法函数
来看看这是什么样子

```
let addition = (a, b) => a + b 
```

```
const addition = (a, b) => a + b 
```

看起来差不多，对吧？那么区别在哪里呢？
嗯，因为 JavaScript 是在运行时计算的，所以这种类型的`a`和`b`可以是任何类型，如果是字符串或布尔值，就会发生一些奇怪的 JavaScript 事情

**但是嘿，博客先生！？**
reason ml 代码都一样？那怎么会更好呢？
因为它会在运行前编译。这意味着类型将被推断为整数，如果你使用这个函数并给它一个字符串，编译器将会生气。
**但如何？**如果我写了`+.`的话，类型会被推断为浮点型。

此外，由于语言是编译的，您可以切换编译器。“标准”的 ReasonML 编译器是 [bucklescript](https://bucklescript.github.io/) ，它编译成 JavaScript，因此可以在浏览器中运行。然而，有些编译器可以编译成字节码，并且可以在任何操作系统上运行。这使得 Reason 开发人员能够利用他们良好的网络技能编写各种简洁的本地应用程序。然而，这部分原因仍然非常年轻，还没有准备好广泛的生产使用。

## 差异

有些事情并不与 JavaScript 一一对应。其中之一就是开关。在 ReasonML 中，switch 是一个非常强大的模式匹配工具，可以帮助你做各种各样很好的条件逻辑，并且开销为零。为了抛开“但是 javascript 中的 switch 很糟糕”的争论，我将展示一个简单的推理在 JavaScript 中如何编译。
还有比 FizzBuzz
更好的例子吗

```
let fizzbuzz = (i) =>
  switch (i mod 3, i mod 5) {
  | (0, 0) => "FizzBuzz"
  | (0, _) => "Fizz"
  | (_, 0) => "Buzz"
  | _ => string_of_int(i)
  }; 
```

现在，当我们谈论阅读编译代码时，我们通常会不寒而栗。但是说到编译的原因，它并没有那么糟糕。

```
function fizzbuzz(i) {
  var match = i % 3;
  var match$1 = i % 5;
  if (match !== 0) {
    if (match$1 !== 0) {
      return String(i);
    } else {
      return "Buzz";
    }
  } else if (match$1 !== 0) {
    return "Fizz";
  } else {
    return "FizzBuzz";
  }
} 
```

尽管输出非常冗长，但是非常容易阅读。我将把分析这两者之间的区别留给读者，但我只是暗示一下,`_`是一个通配符，而`|`是语句中的一个分隔符。

下周我将向你展示我如何使用 ReasonML 和 React 为我的妻子制作一个针织应用程序。