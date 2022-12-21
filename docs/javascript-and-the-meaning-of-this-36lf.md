# Javascript 及其含义

> 原文：<https://dev.to/bagwaa/javascript-and-the-meaning-of-this-36lf>

谈到 Javascript 中的`this`的含义，它可能是最难学的东西之一，也是许多错误和问题的来源。在这篇文章中，我想谈谈如何判断`this`关键字指的是什么，以及在什么情况下它可能不是你所期望的。

## 弄清楚`this`的意思

有四种方法可以判断出`this`关键字在您的 Javascript 中引用了什么，这些方法有一些看似技术性的名称，但请继续关注我，因为它们非常简单。

*   隐式结合
*   显式绑定
*   新绑定
*   窗口绑定

## 什么是绑定？

Javascript 中的绑定指的是我们可以将我们的函数调用绑定到特定的上下文，我们可以隐式地这样做，让 Javascript 为我们做它的事情，或者我们可以非常明确地让 Javascript 确切地知道我们希望`this`关键字引用什么。

如果这没有意义，不要惊慌，因为我将通过一些例子详细阐述这些概念中的每一个，让我们先看看最基本的例子。

## 隐含绑定

正如你所看到的，我们有一个 Javascript 对象，它有三个属性，一个`name`，一个`age`和一个`sayName()`函数。

```
const me = {
  name: "Richard",
  age: 42,
  sayName() {
    console.log(this.name)
  },
}

me.sayName() 
```

在`sayName()`函数中，我们使用`console.log()`将`name`属性打印出来，但是`this`指的是什么呢？

理解`this`关键字所指的关键是查看调用发生的位置，在大多数情况下，您可以简单地看向点的左边，并确切地看到`this`将引用什么。

```
me.sayName() 
```

很明显，在这种特殊情况下，`this`关键字引用了`me`对象，这被称为**隐式绑定**，因为我们从未声明我们想要这样，它只是作为 Javascript 的一部分隐式发生。

在大多数情况下，这就是你将在 Javascript 中看到的，然而，也有一些情况，特别是在 React 组件中，我们必须明确我们的绑定。

## 显式绑定

显式绑定是指作为一名开发人员，您显式地定义您希望`this`关键字在您的 Javascript 中引用什么，这可以使用三个独立的函数来完成，它们都为我们提供了绑定到上下文的能力，但方式略有不同。

*   `call()`
*   `apply()`
*   `bind()`

## 调用()

让我们先来看一个代码示例，然后我会解释我们用`call()`方法到底在看什么。

```
const sayName = function() {
  console.log(this.name)
}

const author = {
  name: "Richard",
  age: 42,
}

sayName.call(author) // Outputs Richard 
```

在这个例子中，我们从声明一个简单的 Javascript 函数开始，这个函数不是任何对象的一部分，正如你所看到的，关键字`this`被用在函数中。

我们还有一个包含一个`name`和一个`age`的`author`对象。

如果我们单独调用`sayName()`函数，它会抛出一个错误，因为在函数内部，`this`关键字没有引用任何东西。

我们可以通过使用`call()`函数显式地设置`this`关键字指向什么。像这样使用`call()`函数给了我们的`sayName()`函数一个工作环境，并将`this`指向`author`。

```
sayName.call(author) // Outputs Richard 
```

我们也可以用`call()`函数提供参数，让我们看看另一个例子

```
const sayName = function(food1, food2, food3) {
  console.log(this.name + ` likes ${food1}, ${food2}, and ${food3}`)
}

const author = {
  name: "Richard",
  age: 42,
}

const food = ["Pizza", "Tacos", "Lasagne"]

sayName.call(author, food[0], food[1], food[2]) 
```

像以前一样，我们调用`sayName()`函数，并使用`call()`提供我们希望调用它的上下文，在本例中是`author`。

然后我们传递从数组`food`中取出的三个参数，正如你所看到的，这些参数像常规参数一样被传递到`sayName()`函数中，这将允许我们将它们`console.log()`出来。

## 应用()

你可能不喜欢的一件事是将所有这些单独的`food`项与我们希望使用的上下文一起传入，当然，直接传入整个数组会更好？

```
const sayName = function(food1, food2, food3) {
  console.log(this.name + ` likes ${food1}, ${food2} and ${food3}`)
}

const author = {
  name: "Richard",
  age: 42,
}

const food = ["Pizza", "Tacos", "Lasagne"]

sayName.apply(author, food) 
```

这正是`apply()`所做的，它与`call()`相同，但不是传入单个的`food`项，我们可以将它们作为一个完整的数组传入，然后调用函数。

## 绑定()

显式绑定的最后一种方法是使用`bind()`函数，`bind()`与`call()`几乎相同，但有一点不同，它不是立即调用函数，而是简单地返回一个具有所需绑定的新函数，让我们看一个例子。

```
const sayName = function(food1, food2, food3) {
  console.log(this.name + ` likes ${food1}, ${food2}, and ${food3}`)
}

const author = {
  name: "Richard",
  age: 42,
}

const food = ["Pizza", "Tacos", "Lasagne"]

const newFn = sayName.bind(author, food[0], food[1], food[2])

newFn() // "Richard likes Pizza, Tacos, and Lasagne" 
```

正如你所看到的，这与`call()`的工作方式完全一样，只是没有立即调用函数，在这种情况下，新函数被赋给一个名为`newFn`的常数，然后我们在之后的行中调用它(我们可以在任何时候调用它)。这种特殊类型的绑定经常出现在 React 组件中，但是我将在稍后详细介绍。

## 新绑定

`new`绑定很容易解释，任何时候你在 Javascript 中使用`new`关键字，你都是在将`this`绑定到你正在创建的新对象，下面是一个简单的例子。

```
const Book = function(title, author, isbn) {
  this.title = title
  this.author = author
  this.isbn = isbn
}

const greatBook = new Book("Refactoring", "Martin Fowler", "0-201-48567-2")

console.log(greatBook) 
```

上面的输出将是我们创建的新对象，它包含使用关键字
设置的`title`、`author`和`ISBN`

```
[object  Object]  {  author:  "Martin Fowler",  isbn:  "0-201-48567-2",  title:  "Refactoring"  } 
```

## 窗口绑定🤮

我想提到的最后一个绑定是`window`绑定，我不建议使用窗口绑定，因为它本质上污染了全局范围，这可能导致错误和各种问题，但它值得一提，因为它仍然被广泛使用。

一如既往，让我们看一个例子。

```
const sayName = function() {
  console.log(this.name)
}

sayName() // undefined 
```

在这个上下文中,`this`指的是`window`上的全局作用域，调用左边没有任何东西，没有`new`绑定，也没有显式绑定。理论上，我们可以通过在`window`上指定缺失的`name`属性来解决这个问题，但是请不要这样做

```
const sayName = function() {
  console.log(this.name)
}
window.name = "Richard"
sayName() // "Richard" 
```

## 为什么关于所有`this`装订的知识知道了有用？

在我们的 React 组件中，我们经常必须明确我们的绑定，这是因为在从我们的 JSX 中调用时，我们不知道上下文是什么，并且由于组件本质上可以在任何地方重用，所以它可能不总是相同的。

看一下这个例子，在`constructor()`的末尾，你可以看到我们将`switchOff()`方法显式绑定到 React 组件。

```
class App extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      mode: "on",
    }

    this.switchOff = this.switchOff.bind(this)
  }
  switchOff() {
    this.setState({
      mode: "off",
    })
  }
  render() {
    const { mode } = this.state

    return (
      <div>
        {mode === "on" && <button onClick={this.switchOff}>Switch Off</button>}
      </div>
    )
  }
}

ReactDOM.render(<App />, document.getElementById("app")) 
```

在上面的例子中，我们有一个简单的 React 组件，最重要的一行是我们使用下面的
将`bind()`关键字`this`添加到这个特定组件的`constructor()`的末尾

```
this.switchOff = this.switchOff.bind(this) 
```

如果没有这一行，`switchOff()`函数将不知道要调用什么上下文，因为它是从 JSX 调用的。

## 结论

正如你所看到的，当我们单独看每个概念时，`this`关键字很容易理解，我认为困惑产生于这些似乎在 Javascript 中随处可见的小“陷阱”。