# 分解 ES6:展开运算符

> 原文：<https://dev.to/torianne02/breaking-down-es6-spread-operator-k1m>

上周我开始了这个博客系列，通过写`let`和`const`来分解 ES6 中引入的新工具。本周我将跳转到传播运算符，它由三个连续的点表示:`…`。

这个工具有许多用途，我将介绍其中几个重要的用途。然而，出于本文的目的，我将不讨论在对象上使用 spread 操作符，因为这是直到 ES9 才引入的。

所以，让我们开始吧。

## 串联数组

在 JavaScript 中，我们使用`concat()`方法将两个或多个数组组合在一起。使用此方法不会更改现有数组，但会返回一个新数组。

```
let languages = ["JavaScript", "Ruby", "SQL"]
let frameworks = ["React", "Rails"]
let myStack = languages.concat(frameworks)

console.log(myStack) // ["JavaScript", "Ruby", "SQL", "React", "Rails"] 
```

Enter fullscreen mode Exit fullscreen mode

我们可以镜像这个方法，通过使用 spread 操作符而不是`concat()`得到相同的结果。

```
let languages = ["JavaScript", "Ruby", "SQL"]
let frameworks = ["React", "Rails"]
let myStack = [...languages, ...frameworks]

console.log(myStack) // ["JavaScript", "Ruby", "SQL", "React", “Rails"] 
```

Enter fullscreen mode Exit fullscreen mode

*注意:在这种情况下不推荐使用 spread 操作符，因为使用`concat()`相对更快。*

## 复制一个数组

在将一个数组复制到另一个数组时，也就是将新数组的值赋给现有数组时，spread 运算符是一个非常好用的工具。在 ES6 和 spread 操作符引入之前，您可以实现这一点，但是有一个小问题。如果您尝试更改新阵列，也会影响原始阵列。

```
let myStack = ["JavaScript", "Ruby"]
let myStack2 = myStack

console.log(myStack2) // ["JavaScript", "Ruby"]
myStack2.push("SQL")
console.log(myStack) // ["JavaScript", "Ruby", "SQL"] 
```

Enter fullscreen mode Exit fullscreen mode

有了 spread 运算符，这个问题就解决了。您可以安全地更改新数组，而无需更改原始数组。

```
let myStack = ["JavaScript", "Ruby"]
let myStack2 = [...myStack]

console.log(myStack2) // ["JavaScript", "Ruby"]
myStack2.push("SQL")
console.log(myStack) // ["JavaScript", "Ruby"] 
```

Enter fullscreen mode Exit fullscreen mode

## 展开一个数组

为了扩展一个数组，也就是在任意给定位置将一个数组插入到另一个数组中，并保持生成的数组平坦，我们需要使用 spread 操作符。在 ES6 之前，如果您扩展一个数组，最终会得到一个嵌套的数组。

```
let myStack = ["JavaScript", "Ruby"]
let techLanguages = ["Python", myStack, "Java"]

console.log(techLanguages) // ["Python", ["JavaScript", "Ruby"], “Java"] 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，spread 操作符使扩展数组变得容易，而无需创建嵌套数组。

```
let myStack = ["JavaScript", "Ruby"]
let techLanguages = ["Python", ...myStack, "Java"]

console.log(techLanguages) // ["Python", "JavaScript", "Ruby", “Java"] 
```

Enter fullscreen mode Exit fullscreen mode

## 串成数组

spread 操作符的一个巧妙之处在于，你可以将一个字符串转换成一个数组。我一时想不出它有什么好的使用案例，但是我想我会把这个有趣的小故事包括进来。

```
let ruby = "Ruby"
let char = [...ruby]

console.log(char) // ["R", "u", "b", “y"] 
```

Enter fullscreen mode Exit fullscreen mode

## 数学

spread 操作符使得在`Math`对象方法中放置数组成为可能。如果不使用扩展操作符，输出将是`NaN`。

```
let nums = [1, 2, 5, 9]

Math.max(nums) // NaN 
```

Enter fullscreen mode Exit fullscreen mode

当使用 spread 运算符时，它允许将数组的内容作为数字列表而不是数组来读取。

```
let nums = [1, 3, 5, 7, 9, 11, 13]

Math.max(…nums) // 13 
```

Enter fullscreen mode Exit fullscreen mode

### 最后的想法

虽然我没有介绍 spread 操作符的每一个方面，但我确实介绍了它的一些最流行的用例。对于阅读本文的读者，请随意展开讨论，并提及 spread 操作符的其他用途。

#### 来源

[JavaScript | Spread Operator](https://www.geeksforgeeks.org/javascript-spread-operator/)
[Spread Operator](https://davidwalsh.name/spread-operator)
[6 大用途重组和 ES6 Spread Operator](https://codeburst.io/a-simple-guide-to-destructuring-and-es6-spread-operator-e02212af5831)
[JavaScript ES6-Spread 语法(…)](https://codeburst.io/javascript-es6-the-spread-syntax-f5c35525f754)
[Spread 语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
[[ECMAScript-es 2018 中的对象 Rest/Spread 属性(ES9)](https://nitayneeman.com/posts/object-rest-and-spread-properties-in-ecmascript-2018/)