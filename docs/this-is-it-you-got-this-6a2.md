# 就是这个！你有“这个”

> 原文：<https://dev.to/shimphillip/this-is-it-you-got-this-6a2>

关键字必定是最令人困惑的 JavaScript 概念之一，因为它的调用方式是动态的。如果你发现自己在读这篇文章，你很可能已经碰到过几次`this`，想要有更好的理解。我希望用实际例子和最少的术语把`this`讲得非常清楚。请确保在您最喜欢的浏览器中打开您的控制台，然后继续操作！

* * *

## `this`作全局

识别`this`最直接的方法是当它被用作全局对象时。全局对象是可以从 JavaScript 代码中的任何地方访问的对象。浏览器称这个全局对象为`window`，NodeJS 称之为`global`。这个基本的默认绑定被称为**默认绑定**。

```
console.log(this) // window
console.log(window) // window
console.log(this === window) // true 
```

*你知道`fetch`和`localStorage`是全局窗口对象的一部分吗？*

用`var`关键字和函数声明声明的变量成为窗口对象的属性。可以在没有点或括号符号的情况下访问全局对象的属性。然而，使用`let`或`const`关键字声明的变量不会成为全局对象的一部分，相反，它们将被存储在一个不可访问的环境中。

```
var age = 29
console.log(age) // 29
console.log(this.age) // 29
console.log(window.age) // 29

function sayHi() {
  console.log("Hi")
}
sayHi() // Hi
this.sayHi() // Hi
window.sayHi() // Hi

const sayHello = function() {
  console.log("Hello")
}
sayHello() // Hello
this.sayHello() // Uncaught TypeError: this.sayHello is not a function
window.sayHello() // Uncaught TypeError: window.sayHello is not a function 
```

* * *

## 功能中的`this`

常规函数内部也引用全局对象。或者，我们也可以说这些函数的**上下文**是全局对象。上下文仅仅意味着在 javascript 引擎运行代码的给定时刻`this`的值(这也称为“执行上下文”)。

```
var whatIsThis = function() {
  console.log(this)
}

whatIsThis() // window 
```

注意:当**使用严格的**模式时，它不允许`this`默认绑定到`window`对象。因此，`this`的值导致 undefined。

```
"use strict"

var whatIsThis = function() {
  console.log(this)
}

whatIsThis() // undefined 
```

* * *

## `this`在战法上

方法意味着它是一个对象内部的函数。 <mark>`this`关键字在方法内部被设置为其父对象。</mark>这被称为隐式绑定，因为`this`被间接绑定到它所属的对象。

```
var obj = {
  getThis: function() {
    return this
  },
}

// equivalent ES6 method
var obj = {
  getThis() {
    return this
  },
}

console.log(obj.getThis()) // obj
console.log(obj === obj.getThis()) // true 
```

要访问方法中对象的属性，需要显式使用`this`。否则，它将在同一个函数范围内寻找同名的变量。

```
var me = {
  name: "Phillip",
  getName() {
    const name = "Sunnie"
    console.log("My name is " + this.name)
    console.log("My name is " + name)
  },
}

me.getName() // My name is Phillip
// My name is Sunnie 
```

`me.getName()`给出预期的字符串。如果我们将 getName 方法的定义赋给方法之外的变量会怎么样？这将导致`this`的隐式绑定丢失，因为新的 getName 函数不再绑定到“me”对象。相反，因为我们的新 getName 是用`var`关键字声明的，所以它被绑定到全局窗口对象，并将尝试将`this.name`作为全局对象的属性进行搜索。以下是 MDN 官方文件对此事的说法:

> 在大多数情况下，这个值是由函数的调用方式决定的。在执行过程中不能通过赋值来设置，可能每次调用函数都不一样。

```
var me = {
  name: "Phillip",
  getName: function() {
    console.log("My name is " + this.name)
  },
}

var getName = me.getName
me.getName() // My name is Phillip
getName() // My name is undefined 
```

*您可能会看到“我的名字是”，而不是“我的名字未定义”。这是因为我们之前在全局范围内使用了`this.name`，所以它的键在那里，但是它的值被设置为一个空字符串*

我们把 getName 方法从“me”对象中提取出来，使它成为一个独立的函数，怎么样？然后在“me”对象中创建另一个同名的 getName 属性，并将独立的 getName 函数指定为引用。让我们试着分别给他们打电话。如果我们单独调用独立函数，正如您之前观察到的，`this`将引用全局对象，并尝试从`window`对象中搜索名称。如果你调用函数作为‘我’对象的属性，`this`的上下文将是‘我’对象。

```
function getName() {
  console.log("My name is " + this.name)
}

var me = {
  name: "Phillip",
  getName: getName,
}

getName() // My name is undefined
me.getName() // My name is Phillip 
```

*经验法则:看看你的方法的左边，它被叫做，`this`属于那个对象。如果没有，`this`属于全局对象。*

* * *

## `this`使用调用、应用和绑定。

我们想让我们的“getName”函数更具可重用性。让我们通过使用调用、应用和绑定函数来改进我们的代码。这些是附加到函数定义并直接调用它们的特殊函数。`call`和`apply`将对象作为第一个参数，因此方法将理解如何处理`this`。如果没有传递参数，上下文将是全局对象。

```
function getName() {
    console.log("My name is " + this.name)
}

var me = {
  name: "Phillip",
}

var you = {
  name: "Sunnie"
}

getName.call(me) // My name is Phillip
getName.apply(you) // My name is Sunnie
getName.call() // My name is undefined 
```

`call`和`apply`的区别在于如何在第一个参数之后向方法传递几个参数。`call`将使用逗号分隔值，`apply`也将使用逗号分隔值，但在数组内部。

```
function getInfo(age, hobby) {
    console.log(`My name is ${this.name}, I am ${age} and my hobby is ${hobby}`)
}

var me = {
  name: "Phillip",
}

var you = {
  name: "Sunnie"
}

getInfo.call(me, 29, "coding") // My name is Phillip, I am 29 and my hobby is coding
getInfo.apply(you, [28, "floral design"]) // My name is Sunnie, I am 28 and my hobby is floral design 
```

*有用的提示: **C** omma 接收**C**omma 和**A**apply 接收 **a** rray*

通常，我们只想将某些方法与某些对象相关联。`bind`帮助我们将一个特定的方法链接到一个特定的对象，这样`this`的值是可预测的，并且可以通过查看它的定义而不是调查它是如何被调用的来找到。与调用和应用不同，绑定不会立即调用其附属函数。这叫做**显式绑定**

```
function getName() {
  console.log("My name is " + this.name)
}

var me = {
  name: "Phillip",
}

getName = getName.bind(me)
getName() // My name is Phillip 
```

在使用 bind 之后，不要忘记将它重新赋值给这个方法！

bind 的实际用途是当函数作为回调传递时。让我们看一个不使用 bind 的例子。

```
var me = {
  name: "Phillip",
  getName: function() {
    console.log("My name is " + this.name)
  },
}

function calleMeMaybe(callback) {
  callback()
}

calleMeMaybe(me.getName) // My name is undefined 
```

getName 是未定义的，因为在引擎盖下，这就是所发生的事情。

```
callback = me.getName 
```

现在让我们将 getName 绑定到“me”对象。

```
var me = {
  name: "Phillip",
  getName: function() {
    console.log("My name is " + this.name)
  },
}
me.getName = me.getName.bind(me)

function calleMeMaybe(callback) {
  callback()
}

calleMeMaybe(me.getName) // My name Phillip 
```

* * *

## `this`在函数构造函数中

在 JavaScript 中，函数可以作为构造函数来使用“new”关键字构建新对象。`this`将被设置为由函数构造器创建的实例(或对象)。

```
function Song(author) {
  this.author = author
  this.song = "Let it go"
  this.getInfo = function() {
    console.log(`${this.author} sang ${this.song}`)
  }
}

var song = new Song("Idina Menzel")
song.getInfo() // Idina Menzel sang Let it go

var song1 = new Song("Demi Lovato")
song1.getInfo() // Demi Lovato sang Let it go

// change of context
var getInfo = song1.getInfo 
getInfo() // undefined is sung by undefined 
```

* * *

## `this`同课

类是现代的函数构造器。在类内部，`this`的行为与函数构造函数相同，并且引用由类创建的特定对象。注意“严格模式”默认应用于类。

```
class Song {
  constructor(author) {
    this.author = author
    this.song = "Let it go"
  }

  getInfo() {
    console.log(`${this.song} is sung by ${this.author}`)
  }
}

const song = new Song("Idina Menzel")
song.getInfo() // Idina Menzel sang Let it go

const song1 = new Song("Demi Lovato")
song1.getInfo() // Demi Lovato sang Let it go

// change of context
const getInfo = song1.getInfo
getInfo() // Uncaught TypeError: Cannot read property 'song' of undefined 
```

如果您熟悉 React，您会注意到我们将事件处理程序与类本身显式绑定。这是因为事件处理方法被一些事件监听器作为回调传递，比如 onClick、onSubmit 等...记住，回调函数会丢失它们的上下文。

```
class Form extends React.Component {
  constructor(props) {
    super(props)
    this.handleClick = this.handleClick.bind(this) // necessary
  }

  handleSubmit(event) {
    // Handle logic
  }

  render() {
    return <button onClick={this.handleSubmit}>Submit</button>
  }
} 
```

* * *

## 箭头功能中的`this`

JavaScript 可能会变得古怪并导致意外的行为。你认为`this`的值在方法内部的函数中会是多少？我们的直觉告诉我们，它仍然会引用该方法所属的父对象。然而，事实并非如此。`this`实际上会指代`window`对象。让我们拿前面的例子来修改一下。

```
var me = {
  name: "Phillip",
  getName: function () {
    function innerFunction() {
      console.log("My name is " + this.name)
    }
    innerFunction();
  },
}

me.getName() // My name is undefined 
```

有多种方法可以解决这个问题。

1.  我们可以将`this`存储到一个变量中，并在内部函数中引用该变量。按照惯例，这个变量叫做‘自我’。
2.  我们可以在方法内部使用`bind`将内部函数连接到方法的上下文。
3.  或者使用箭头功能。

> 在箭头函数中，`this`保留封闭词法上下文的`this`的值。- MDN

意味着`this`的值被设置为包含箭头函数的函数。

```
// 1
var me = {
  name: "Phillip",
  getName: function () {
    var self = this;
    function innerFunction() {
      console.log("My name is " + self.name)
    }
    innerFunction();
  },
}

me.getName()

// 2
var me = {
  name: "Phillip",
  getName: function () {
    function innerFunction() {
      console.log("My name is " + this.name)
    }
    innerFunction = innerFunction.bind(this);
    innerFunction();
  },
}

me.getName()

//3
var me = {
  name: "Phillip",
  getName: function () {
    const innerFunction = () => {
      console.log("My name is " + this.name)
    }
    innerFunction();
  },
}

me.getName() 
```

* * *

## `this`同 HTML

当事件被触发时，JavaScript 事件监听器可以访问`this`。`this`然后会引用导致它的 HTML 元素。如果事件监听器回调函数被声明为一个箭头函数，`this`将引用窗口对象，它的封闭上下文。

```
<h1 class="h1">Hello World</h1>
<h2 class="h2">Hi World</h2> 
```

```
const h1 = document.querySelector(".h1")
const h2 = document.querySelector(".h2")

h1.addEventListener("click", function(e) {
  console.log(e.target) // <h1 class="h1">Hello World</h1>
  console.log(this) // <h1 class="h1">Hello World</h1>
})

h2.addEventListener("click", e => {
  console.log(e.target) // <h2 class="h2">Hi World</h2>
  console.log(this) // window
}) 
```

* * *

## 总结

谢谢你看我的文章！👏👏我真诚地希望这是有帮助的内容，以消除您对`this`关键字的任何困惑。我将带着我的长篇文章的摘要离开。再次感谢！

*   `this`默认情况下一般指全局对象，在普通函数中:默认绑定。
*   在严格模式下，`this`未定义。
*   在方法内部，`this`是拥有该方法的对象，但这取决于如何调用`this`。看左边的`this`来确定它的价值。
*   使用 call，apply 显式调用带有所需对象的方法。使用 bind 将`this`粘合到一个特定的对象上。
*   在箭头函数中，查看封闭上下文来确定`this`的值