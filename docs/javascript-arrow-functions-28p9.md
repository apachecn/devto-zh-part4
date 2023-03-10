# 为什么应该在 React 中使用箭头函数

> 原文：<https://dev.to/austinbh/javascript-arrow-functions-28p9>

我用 Javascript 工作的时间越多，我就越喜欢这门语言本身。几周前，当我第一次学习 Javascript 时，我们接触了一点箭头函数。我知道如何写它们，也知道它们做了些什么。然而，直到我们开始进入 react，我才真正了解到箭头函数可以做什么，以及为什么它们如此有优势。

## 什么是箭头功能

箭头函数是 Javascript 中的一种函数语法，乍一看，它似乎是一种简化的函数表达式。

```
"Function Expression"

let foo = function foo(bar) {return bar + 1}
--------------------------------
"Arrow Function Expression"

let foo = bar => bar + 1 
```

在我们讨论 arrow 函数缺少返回之前，让我们先比较一下这两个函数表达式。这两个函数都将接受 1 个参数，然后返回该参数+ 1。

如您所见，arrow 函数是编写函数表达式的一种更短的方式。arrow 函数的一个优点是能够进一步压缩语法。在上面的函数中，我既没有将参数括在括号中，也没有提供 return 语句。

单参数函数的隐式返回和可选括号对于我们的 arrow 函数来说都是很好的语法糖。尽管我们不需要为单参数函数提供括号，但是如果我们的函数接受更多或更少的参数，我们就需要括号。

## 为什么箭头功能很重要

关于箭头函数的最重要的部分之一是另一个隐含的动作，从箭头函数来看并不明显。这是对“this”关键字的保留。

对于 Javascript 中的标准函数表达式或声明,“this”的定义取决于调用函数的位置。因此，为了确保“this”作用于正确的对象或类，您可能需要使用 bind 方法来确保“this”关键字保持正确的引用。

```
let foo = {
   bar: 50,
   getBar: function() {
      return this.bar
   }
}

let unboundGetBar = foo.getBar
console.log(unboundGetBar())
//=> undefined

let boundGetBar = unboundGetBar.bind(foo)
console.log(boundGetBar())
//=> 50 
```

我们必须使用 bind 方法来告诉 getBar‘this’应该引用什么。当 getBar 在第一个例子中没有被绑定时，它从全局范围继承了“this”的定义，结果控制台日志返回 undefined。使用箭头函数，我们不必担心绑定“this ”,因为箭头函数没有自己的“this”。因此，箭头函数将从其封闭范围继承“this”。

## 箭头功能在 React

在反应中，这可能是一个问题。当编写类组件时，您写出的所有函数都需要绑定到构造函数，以便“this”关键字以适当的方式运行，从而允许您正确地呈现数据。

如果您需要在几个不同的函数中使用“This”关键字，这会看起来有点混乱。此时，arrow 函数会突然出现以挽救局面。

```
import React from 'react';

export default class App extends Component {
   constructor() {
      super()
         this.exampleOne = this.exampleOne.bind(this)     
   }

   exampleOne() {
      return this
   }

   exampleTwo = () => {
      return this
   }

   exampleThree() {
      return this
   }

   render() {
      return (
         <h1>{exampleOne()}</h1>
         <h1>{exampleTwo()}</h1>
         <h1>{() => exampleThree()}</h1>
      )
   }
} 
```

示例三将能够返回 this，而无需绑定到构造函数中，因为 arrow 函数表达式允许它从 App 构造函数中继承“this”定义。因此，这允许我们编写 React 函数，而不必显式绑定它。有其他方法来绑定它，另一种保持“this”的方法是编写一个匿名函数来调用你的未绑定函数。

在 render 方法中，我们有一个使用匿名函数保存“this”的例子。我们使用匿名函数来确保‘this’引用适当的信息。虽然上面的三个例子都是确保我们维护“this”的正确定义的方法，但 arrow 函数让您可以编写最少的代码来实现相同的目标。

只要你留意“this”关键字的定义，你就会没事，但是如果你想避免忘记绑定或匿名调用函数的错误，我建议你使用箭头函数。箭头函数可能较新，看起来有点不同，但在某些情况下，它们可能是一个有用的工具。

## 参考文献

*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow _ Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Operators/function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ objects/Function/bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind)
*   [https://reactjs.org/docs/components-and-props.html](https://reactjs.org/docs/components-and-props.html)