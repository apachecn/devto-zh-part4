# 分解 ES6:箭头函数

> 原文：<https://dev.to/torianne02/breaking-down-es6-arrow-functions-m5g>

又到了一周分解四年前推出 ES6 带给我们的工具的时候了。本周我将为你们分解箭头函数。我个人非常喜欢使用箭头函数，因为对我来说，它有助于我的代码看起来更整洁，更容易被其他人阅读和理解。

让我们开始吧！

## 箭头功能

箭头函数是 JavaScript 开发人员中最受欢迎的 ES6 特性之一。箭头函数使用粗箭头，看起来像这样:`=>`。使用箭头函数的最大好处之一是它创建的代码更少，因为语法比普通的函数声明更短。如果使用得当，这种较短的语法可以让其他开发人员更容易理解代码。当谈到使用括号、块`{}`等时，箭头函数有多种语法可用。，这是我将在这篇文章中重点讨论的。

### 无参数

如果您的 arrow 函数中不需要参数，您可以使用空括号来代替参数。

```
const bark = () => “woof woof”
bark() // “woof woof” 
```

Enter fullscreen mode Exit fullscreen mode

虽然这不是正确的做法，也不建议这样做，但是您可以完全去掉括号，仍然会得到相同的结果。

```
const bark = _ => “woof woof”
bark() // “woof woof” 
```

Enter fullscreen mode Exit fullscreen mode

### 单参数

如果您只想在箭头函数中接受一个参数，您可以使用括号

```
const bark = (x) => x
bark(“woof woof”) // “woof woof” 
```

Enter fullscreen mode Exit fullscreen mode

或者你可以不用它们也能达到同样的效果。

```
const bark = x => x
bark(“woof woof”) // “woof woof” 
```

Enter fullscreen mode Exit fullscreen mode

### 多个参数

对于 arrow 函数接受多个参数的情况，您需要使用括号。

```
const add = (num1, num2) => num1 + num2
add(4, 6) // 10 
```

Enter fullscreen mode Exit fullscreen mode

如果省略括号，就会遇到这个错误:`SyntaxError: Missing initializer in const declaration`。你可能会想，“嘿，这是关于使用`const` …”，你是对的。所以，我尝试使用`var`和`let`，结果都是`SyntaxError: Unexpected token =>`。长话短说，如果您在使用多个参数时不包括括号，您将会以某种类型的`SyntaxError`结束。

### 箭头功能用块

对块`{}`使用箭头函数需要一个 return 语句。如果不使用 return 语句，函数的结果将是`undefined`。

```
const subtract = (num1, num2) => {
  num1 - num2
}
subtract(10, 7) // undefined 
```

Enter fullscreen mode Exit fullscreen mode

接下来的两个例子是使用带箭头函数的块的正确方法。

```
const subtract = (num1, num2) => {
  return num1 - num2
}
subtract(10, 7) // 3 
```

Enter fullscreen mode Exit fullscreen mode

```
const isHappy = emotion => {
  if (emotion === “happy”) {
    return true
  } else {
    return false
  }
}
isHappy(“happy”) // true 
```

Enter fullscreen mode Exit fullscreen mode

注意在`isHappy()`的例子中，我没有使用括号。我想这样做是为了继续让大家明白，如果使用一个参数，就没有必要使用括号。

### 对象文字量

还可以使用箭头函数返回对象文本。为了成功地返回一个对象文字，你需要使用括号。

**错了**

```
const printObj = () => { apples: 3 }
printObj() // undefined 
```

Enter fullscreen mode Exit fullscreen mode

**右**

```
const printObj = () => ({ apples: 3 })
printObj() // { apples: 3 } 
```

Enter fullscreen mode Exit fullscreen mode

### 其他值得注意的特征

我没有涵盖箭头函数的所有用例或特征，但是我想简要地介绍一下箭头函数的两个非常重要的方面。我也将提供链接，以便让你进一步研究这个主题。

#### 没有单独的`this`

在 ES6 推出之前，函数不能引用`this`。为了在函数中使用`this`，你必须声明一个带有`this`值的新变量。随着 ES6 的引入，您现在可以在箭头功能中访问`this`。词法范围允许箭头函数访问`this`，即使它在函数之外。

这个功能在推出时对开发者来说是一个巨大的胜利，也是为什么 arrow 函数是 ES6 推出的最受欢迎的工具之一的驱动力之一。

*延伸阅读- [JavaScript:新手用箭头函数](https://codeburst.io/javascript-arrow-functions-for-beginners-926947fc0cdc)T3】*

#### 无递归

箭头函数不允许自引用。这意味着，如果在任何时候你需要使用递归，它将不会工作。所以，如果你需要实现递归，坚持使用普通的函数声明。

### 最后的想法

在创建这个帖子的过程中，我学到了很多以前不知道的关于箭头函数的知识。一般来说，我真的很喜欢使用箭头函数，因为我的代码感觉更干净，更容易阅读。我现在知道，有些情况下我应该避开箭头函数，有些情况下它们绝对是正确的选择。当涉及到箭头函数时，它实际上取决于你的函数的目的，这将因情况而异。

如果你想了解更多关于箭头函数的内容，请随意查看我的资料。另外，如果你对这次讨论有什么要补充的，请随意。

#### 来源

[什么时候(以及为什么)应该使用 ES6 箭头函数——什么时候不应该使用](https://www.freecodecamp.org/news/when-and-why-you-should-use-es6-arrow-functions-and-when-you-shouldnt-3d851d7f0b26/)
[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
[JavaScript:初学者使用的箭头函数](https://codeburst.io/javascript-arrow-functions-for-beginners-926947fc0cdc)