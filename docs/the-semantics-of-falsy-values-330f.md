# 虚假值的语义

> 原文：<https://dev.to/somedood/the-semantics-of-falsy-values-330f>

[![nunocpnp](img/bd15d559e37636c82cd9a0ee46506668.png)](/nunocpnp) [## “空”和“未定义”关键词的区别？

### nuno Pereira Aug 29 ' 191min read

#javascript #webdev #todayilearned #beginners](/nunocpnp/differences-between-null-and-undefined-keywords-2e2m)

最近看了 [@nunocpnp](https://dev.to/nunocpnp) 的这篇文章，讲的是 JavaScript 中`null`和`undefined`的技术差异。读完之后，我意识到 falsy 值的语义是如何容易被忽略的。这两个概念经常被混淆，或者在最糟糕的情况下被互换，这一事实意味着需要更多地了解虚假值背后微妙和细微的语义。

在本文中，我将讨论这一点。我希望通过将 falsy 值的技术差异与它们的语义背景相结合来阐述 [@nunocpnp](https://dev.to/nunocpnp) 的文章。最后，我们可以更好地了解 JavaScript 中许多 falsy 值的正确用法。

当然，这并不意味着我将讨论的所有内容都严格适用于 JavaScript 语言*而仅仅是*。其他语言也有自己的伪结构，但语义相似(如果不是相同的话)。

事不宜迟，我们先从最简单直白的 falsy 值开始:`false`。

# `false`

布尔`false`用于在不满足布尔条件时进行通信。它的用法最适用于支票和[保护条款](https://dev.to/carlillo/refactoring-guard-clauses-4ee6)，其中条件只能是*`true`或`false`——不多也不少。*

 *# 零(`0`)

整数`0`必须*只能*用于数字运算，或者——在罕见的低级情况下——用于[位屏蔽](https://dev.to/somedood/bitmasks-a-very-esoteric-and-impractical-way-of-managing-booleans-1hlf)。数字`0`是*总是*一个数字结构。因此，将它用作布尔构造在语义上是不正确的，并且强烈建议不要使用。

```
// This is good.
function square(num) { return num * num; }

// This is semantically incorrect because the function
// is a boolean condition that checks if a number is odd.
// By interpreting the numerical result of the modulus
// operator as a boolean value, this violates the
// semantics of the `isOddNumber` function.
function isOddNumber(num) { return num % 2; }

// This can be improved by explicitly converting
// the return value to a boolean.
function isOddNumber(num) { return Boolean(num % 2); }

// This also works like the previous example,
// but it looks pretty "hacky" to be completely
// honest. The "double negative operator" uses implicit
// type coercion under the hood, which is not exactly
// desirable if we want our code to be readable,
// maintainable, and semantically correct.
function isOddNumber(num) { return !!(num % 2); } 
```

Enter fullscreen mode Exit fullscreen mode

# 不是数字(`NaN`)

同样的逻辑也适用于`NaN`。值`NaN`严格用于指示失败的数值运算。它可以用作布尔值来检查数值运算是否有效。然而，它不能作为布尔原语`true`和`false`的*鲁莽*替代品。

```
// This is good. There is no need to explicitly
// convert `NaN` to `false` because the function
// is a numerical operation that works fine except
// for a few edge cases (when y = 0). Semantics is
// still preserved by the returned number or `NaN`.
function divide(x, y) { return x / y; }

// This is semantically incorrect because `NaN` is
// recklessly used where `false` is already sufficient.
function canVoteInElections(person) {
  return (person.age > 18 && person.isCitizen)
    ? true : NaN;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 空数组(`[]`)和空字符串(`''`)

尽管根据语言规范的规定，空数组实际上**不是**falsy 值[，但我仍然认为它们在语义上是*falsy，如果这有意义的话。此外，由于字符串在技术上只是单个字符的数组，因此空字符串也是一个 falsy 值。奇怪的是，空字符串确实是一个 falsy 值(根据前面提到的语言规范中的部分),尽管空数组不是这样。*](http://www.ecma-international.org/ecma-262/6.0/#sec-toboolean)

尽管如此，空数组和空字符串*不能被隐式地解释为布尔值。它们只能在数组和字符串操作的上下文中返回。*

例如，如果一个操作恰好过滤掉了一个空数组的所有元素，那么这个空数组就会被返回。如果给定数组的所有元素都满足特定的过滤条件，函数将返回一个空数组。在应用了一个碰巧消除了所有元素的过滤器后，返回一个空数组而不是一些其他的假值(如`false`或`NaN`)更有意义，因为得到的空数组意味着它是从以前的数组中过滤出来的。

一个完整的玩具箱可以作为一个相关的类比。玩具箱代表一个数组。从玩具箱中取出所有玩具的动作代表过滤过程。经过一个过滤过程，剩下一个空的玩具箱也就说得通了。

然而，如果一个人真的坚持根据数组是否为空来将其解释为布尔类型，那么最好使用 [`Array#length`属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/length)。然而，由于它返回一个整数值，一个语义上正确的(尽管相当迂腐)实现需要显式转换为一个布尔原语。

```
// This is semantically correct.
function isEmptyArray(arr) { return !Boolean(arr.length); }

// This is also correct, but without the indication
// of an explicit conversion, this has lesser semantic
// meaning than its unabbreviated variation above.
function isEmptyArray(arr) { return !arr.length; }

// This is okay...
function logEmptyString(str) {
  if (!str)
    console.log(str);
}

// ... but this is better.
function logEmptyString(str) {
  if (str === '')
    console.log(str);
} 
```

Enter fullscreen mode Exit fullscreen mode

# 空物体(`{}`)和`null`

就像空数组一样，空对象被语言规范认为是“真的”。为了这篇文章，我也将它们视为语义上的错误。

空对象遵循与空数组相同的推理。它们只能作为某些对象操作的结果返回。它们**不能**作为*鲁莽*布尔原语的替代品。

幸运的是，存在一个 falsy 布尔结构，字面意思是*无* : `null`。如果一个对象操作产生一个空对象，有时返回`null`更合适。

例如，搜索对象集合的函数如果搜索失败，可以返回`null`。就语义而言，字面上返回 *nothing* 比一个空对象更有意义。此外，由于所有的*对象都是真的，而只有`null`是假的，这样的搜索功能可以避开显式的布尔转换。语义正确的对象搜索函数的一个例子是 [`document.getElementById`](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById) 。*

简而言之，`null`的语义围绕着这样一个事实，即它是绝对*无*的有意和明确的表示。你可以认为它是一个比空物体更“空”的物体。在这种情况下，为什么`typeof null`返回`'object'` ~~突然变得更有意义了，尽管从~~开始就是一个错误。

# `undefined`

顾名思义，`undefined`严格来说是一个占位符，代表*没有在程序中定义*的东西，而`null`是一个占位符，代表*根本不存在*的东西。

如果要从对象搜索函数中故意返回`undefined`，它会破坏`null`的语义，后者传达了绝对不返回*任何东西*的明确意图。通过返回`undefined`，正在讨论的搜索函数返回的是*尚未定义的东西*，而不是*不存在的东西*。

更具体地说，我们假设如果一个给定 ID 为*的 HTML 元素在当前 [`document`](https://developer.mozilla.org/en-US/docs/Web/API/Document) 中不存在*，那么 [`document.getElementById`](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById) 返回`undefined`。这听起来不是很奇怪吗？

正因如此，在返回 *nothing* 时`null`比`undefined`更正确、更可取。尽管这两个词基本上表示相同的概念*虚无*，但语言中的微妙之处完全改变了它们的语义。

# 结论

在编程中，语义是一个特别令人厌烦的主题，因为它不会显著影响程序的行为，但它在代码的可读性和可维护性方面起着巨大的作用。

如`null`和`undefined`所示，尽管两个构造表示相同的想法，但它们在语义上可能不同。正是由于这个原因，我们必须意识到这些细微差别，以便编写更加一致和可维护的代码。

根据一般经验，必须在正确的上下文中使用和返回 falsy 类型。不鼓励依赖隐式类型强制，因为它不尊重数据类型的语义。在转换类型时，尤其是那些本质上是布尔型的类型，显式转换在语义上总是更好。*