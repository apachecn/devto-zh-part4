# 闭包:JS 范围攻击

> 原文：<https://dev.to/bbarbour/closures-the-js-scope-hack-3365>

当一个函数被调用并完成任务后，它将从调用堆栈中移除。被移除意味着它的可变环境也应该是吧？

并非在所有情况下都是如此，因为 Javascript 中存在**闭包**。

理解闭包是如何工作的将有助于你利用它们的能力，成为更好的 Javascript 程序员。见鬼，在我完全理解闭包之前，我经常碰到它们。我认为很多新程序员倾向于这样做，跟随例子和代码。这是这门语言的一个容易被忽略的特性，但是在合适的人手里却很神奇。

在开始之前，了解一下赋予闭包强大功能的两个主要因素背后的背景是很重要的。

闭包的第一个特点是函数是“一等公民”你可能以前听说过这个词，这个词总是被随意使用。我自己也觉得它含糊不清，只是暗示了功能的重要性，而不是机械地表达它的意思。

函数的行为类似于数据类型。因为，归根结底，它们是物体。Javascript 中的大多数结构都是如此。因为他们的对象可以被操纵。您可以将函数赋给变量，从而创建函数表达式。函数也可以作为参数传递给其他函数或作为值返回。做这些动作中的任何一个都是创建高阶函数的方法。

这里有一个高阶函数的简单例子，作为复习。

```
const calculateSalesTax = stateTaxRate => {
  return cost => {
    return cost * stateTaxRate
  }
}

const newYorkTax = calculateSalesTax(1.08875)
const northCarolinaTax = calculateSalesTax(1.0475)

console.log(newYorkTax(30.0).toFixed(2)) // returns 32.66
console.log(northCarolinaTax(30.0).toFixed(2)) // returns 31.43 
```

Enter fullscreen mode Exit fullscreen mode

第一个箭头函数被分配给变量`calculateSalesTax`，当被调用时，它启动另一个箭头函数，该函数将成本作为参数，并给出最终的计算结果。我们重用这个函数来计算两个不同州的销售税。帮助我们的代码保持干燥。

词法范围允许 Javascript 引擎知道，甚至在我们运行代码之前，每个函数可以访问哪些变量。“词法”部分意味着它基于*代码中的*内容，而不是它们运行的位置。

我认为闭包是函数中的小容器，变量被放入其中等待被相同作用域内的函数访问。

```
const checkSecret = () => {
  const secret = 'pillow' // gets tossed into the bin! 
  return attempt => {
    return secret === attempt
  }
}

const attempt = checkSecret()

console.log(attempt('blanket')) // returns false
console.log(attempt('pillow')) // returns true

console.log(checkSecret()) // Returns Function. Note: We can't get to our secret variable! Probably a good thing, since its secret after all. 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子展示了一个正在运行的闭包。即使在`checkSecret`已经运行并返回了 arrow 函数之后，Javascript 仍然可以到达 bin 并抓取`secret`变量来进行比较。返回的函数抛出我们的布尔值。

上面的例子也说明了闭包的一个主要优点。保护变量！我们根本无法从外部访问`secret`，只有 Javascript 引擎可以。

我在一门课上看到有人描述了这个概念。基本上，关闭是道德责任。如果父函数产生子函数，它必须提供子函数生存和完成其目的所需的一切。

有了基本的理解，你就可以看到闭包是如何对我们认为的作用域的工作方式进行了一点修改。我们可以利用它来保护我们的变量。

我们还可以使用闭包的许多其他方法，我计划在以后的博客文章中作为系列文章的一部分进行介绍。所以如果你有兴趣，一定要跟着我。