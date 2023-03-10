# Javascript 中的变量和函数作用域

> 原文：<https://dev.to/bbarbour/var-and-function-scope-in-javascript-3k4p>

是时候讨论范围了。

范围决定了可见和可访问的变量。*块作用域*是一组花括号内的作用域。大多数编程语言都有块范围。也就是说，对于 Javascript 新手来说，什么是*函数作用域*，它是如何工作的？

不知道也没关系。一开始我也不知道。让我解释一下。

顾名思义，功能部分...每当我们在函数内部创建一个`var`时，它就变成了函数的作用域。

示例时间！我们只需要用代码让它更清晰:

```
function dog () {
  var name = "Fido"
}

console.log(name) //name is not defined 
```

Enter fullscreen mode Exit fullscreen mode

您不能在函数外部访问`name`。现在，让我们用积木再试一次。

```
var dog = true
if (dog) {
  var name = "Fido"
}

console.log(name) // returns Fido 
```

Enter fullscreen mode Exit fullscreen mode

即使通过在块内部声明的`name`变量，我们也可以在外部访问。

`let`和`const`这两个关键词是如何发挥作用的？好吧，回到上一个例子，我们将稍微重构一下代码来看看。

```
const dog = true
if (dog) {
  const name = "Fido"
}

console.log(name) //name is not defined 
```

Enter fullscreen mode Exit fullscreen mode

注意:这同样适用于关键字`let`。

该变量不能被访问，因为它的作用域在`if`语句的块中，被困在花括号监狱中，没有逃脱的希望。不像它的哥哥`var`，在这种情况下他是自由的。

对于大多数经验丰富的 Javascript 开发人员来说，这可能是众所周知的基本信息。

我学了 ES6 变量关键词，得到的印象是从来不碰`var`。随着时间的推移，我不再把它看作是过去被禁止的遗物。它是一个工具，就像语言中的所有其他关键字一样。

我一直在扩展关于 Javascript 的知识，深入到具体细节。旅程的一部分一直在玩`var`，以巩固我脑海中的差异。我认为它有助于查看旧代码，或者兼容旧浏览器的代码。

对于那些新手来说，我希望我的解释和例子能帮助你避免让我挠头。

自己摆弄一下关键词，舒服一点。不要害怕！你最终会遇到它，最好像他们说的那样做好准备。

看看[提升](https://dev.to/steelvoltage/hoisting-in-js-the-quick-and-dirty-162m)来更好地感受它是如何工作的，我认为这是理解`var`和功能范围的自然进展。