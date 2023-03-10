# JavaScript 101:箭头函数

> 原文：<https://dev.to/karaluton/javascript-101-arrow-functions-jje>

箭头函数——它们是常规函数的更简洁版本，自从在 ES6 中首次引入以来，它们越来越受欢迎。不仅语法更加简洁，而且它们还提供了隐式返回，我们将深入研究这一点。

> 停下来。如果你没有读过本系列的第一部分， [JavaScript 101:分解函数](https://dev.to/karaluton/javascript-101-breaking-down-functions-4p34)，那么一定要去看看。

让我们从 arrow 函数的语法与常规函数有何不同开始。

这里我们有一个常规函数:

```
function helloWorld(name) {
 console.log('Hello ' + name);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想使用一个箭头函数，它应该是这样的:

```
const helloWorld = name => {
  console.log('Hello ' + name);
} 
```

Enter fullscreen mode Exit fullscreen mode

箭头函数有一些关键的区别。我们已经放弃了使用关键字`function`来声明函数。我们的参数也和以前有所不同。它们现在位于等号之后，粗箭头(`=>`)之前。

你可能也注意到了，我们现在把箭头函数声明为一个变量。那是因为箭头函数是*匿名函数*或者是没有名字声明的函数。您不必将它们赋给变量，但这样做可以让您在出错时更容易地跟踪它们。

> 🌟**快速提示:**如果只有一个参数，括号是可选的。如果你没有任何参数，那么你需要使用空括号。

下面是我们的多参数示例:

```
const helloWorld = (name, emoji) => {
  console.log(emoji + ' Hello ' + name);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们没有参数的例子:

```
const helloWorld = () => {
  console.log('Hello');
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经讨论了箭头函数的语法，让我们来谈谈另一个大的好处——隐式返回！这将使你的代码比以前更短。让我们以我们一直使用的例子为例，切换到 return 而不是 console.log

```
const helloWorld = name => {
  return 'Hello ' + name;
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们只返回一行代码，所以我们可以使用 arrow 函数的能力来进行隐式返回，并像这样重写我们的函数:

```
const helloWorld = name => 'Hello ' + name; 
```

Enter fullscreen mode Exit fullscreen mode

当使用隐式返回时，可以去掉`return`关键字和花括号。这就产生了非常好的单行函数。

隐式返回并不是箭头函数和常规函数之间唯一的区别。另一个大问题是他们如何处理`this`关键字。

在常规函数中，`this`关键字的绑定取决于调用它的上下文。然而，在 arrow 函数内部，`this`是词汇绑定的，这意味着它是静态的，由它所在的范围决定。

这仍然是我试图自己掌握的东西，但是如果你想深入了解，JavaScript Kit 有一个很好的解释。

* * *

一定要在 Twitter 上关注我的许多关于科技的帖子，如果我是诚实的，也有许多关于狗的帖子。