# FizzBuzz 编码挑战

> 原文：<https://dev.to/mattchewone/fizzbuzz-coding-challenge-2m48>

[![Laptop with code](img/f5ff61915f0f258f2a3e01ce1485bd97.png)](///static/e21303dd85ec17c8c68319e1b27dbc4e/33840/image.jpg)

当我在查看积压的视频时，我看了这个[视频](https://www.youtube.com/watch?v=QPZ0pIK_wsc)，它解释了编码面试问题的用法；FizzBuzz 编码面试问题。我想试着自己回答这个问题，并分享我的思考过程。

我在 1:40 左右停止了视频，因为我想自己回答一下，而不是看视频中的答案。

* * *

> 写一个程序打印从 1 到 100 的数字。但是对于三的倍数打印“嘶嘶”而不是数字，对于五的倍数打印“嗡嗡”。对于同时是 3 和 5 的倍数的数字，打印“FizzBuzz”。

* * *

## 我的解决方案

带着上述问题，我首先想到的是如何确定 3 和 5 的倍数。我想到的是[模数/余数运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Remainder_()) `%`我知道 3 的倍数& 5 应该保留余数 0，所以这可以用来确定何时输出正确的文本。

为了开始这个挑战，我使用了 [jsfiddle](https://jsfiddle.net/) ，并创建了以下内容作为我的起点:

```
function fizzbuzz (num) {
  return num
}

for (let i = 1; i <= 100; i++) {
  console.log(fizzbuzz(i))
} 
```

然后我开始我的实现，最初看起来是这样的:

```
function fizzbuzz (num) {
  if (num % 3 === 0 && num % 5 === 0) {
    return `fizzbuzz`
  } else if (num % 3 === 0) {
    return `fizz`
  } else if (num % 5 === 0) {
    return `buzz`
  }

  return num
}

for (let i = 1; i <= 100; i++) {
  console.log(fizzbuzz(i))
} 
```

首先我检查数字是否是 3 和 5 的倍数，如果是，我返回组合的`fizzbuzz`文本，然后我检查数字是否是 3 或 5 的倍数，并输出正确的文本，退回到只返回初始数字。

这是相当冗长的，并有一点重复检查的倍数，所以我想我会改善我的答案。

```
function fizzbuzz (num) {
  const isFizz = num % 3 === 0
  const isBuzz = num % 5 === 0

  if (isFizz && isBuzz) {
    return `fizzbuzz`
  } else if (isFizz) {
    return `fizz`
  } else if (isBuzz) {
    return `buzz`
  }

  return num
}

for (let i = 1; i <= 100; i++) {
  console.log(fizzbuzz(i))
} 
```

因此，这是更好的，但仍然有点冗长，我们已经将重复的检查删除为可以重用的单个检查，但仍然有多个 if 语句。我想，怎样才能使它更简洁明了呢？

下面是我的最终解决方案:

```
function fizzbuzz (num) {
  const isFizz = num % 3 === 0
  const isBuzz = num % 5 === 0

  return `${isFizz ? 'fizz' : ''}${isBuzz ? 'buzz' : ''}` || num
}

for (let i = 1; i <= 100; i++) {
  console.log(fizzbuzz(i))
} 
```

我在[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)中移除了`if statement`以支持[三进制](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)，该模板文字使用[短路](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators)操作符回退到数字。这归结为创建一个字符串，它首先检查数字是否是 3 的倍数，然后添加单词`fizz`，然后检查数字是否是 5 的倍数，然后添加单词`buzz`。这将说明是任一个和两个，并且如果它们都不是真的，它将是一个空字符串。在这种情况下，短路操作符将把空字符串评估为假，并且将使用操作符右边的是初始数字。