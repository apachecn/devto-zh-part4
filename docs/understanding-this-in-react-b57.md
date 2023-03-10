# 理解 React 中的“this”

> 原文：<https://dev.to/akhilaariyachandra/understanding-this-in-react-b57>

> PS -这最初是在我的[博客](https://akhilaariyachandra.com/understanding-this-in-react/)上发布的。如果您想了解 React 和 JavaScript 的更多信息，请查看它！

在 JavaScript 中可能会令人困惑，因为它可能会根据调用它的位置而改变。在这篇文章中，我会试着澄清这个问题。

> JavaScript“this”关键字指的是它所属的对象。 <sup id="fnref1">[1](#fn1)</sup>

为了清楚地理解`this`是如何改变的，让我们看看可以调用它的地方。

## 【孤本】

```
const test = this
console.log(test) // global variable 
```

Enter fullscreen mode Exit fullscreen mode

`this`在函数外部访问将返回全局变量。

## 函数中的“本”

```
function foo() {
  console.log(this) // undefined in “strict mode” or the global variable
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在函数内部访问`this`(不能是方法)，它将是全局变量，在“严格模式”下它将是`undefined`。

## “此”中的一种方法

```
const user = {
  firstName: "Akhila",
  lastName: "Ariyachandra",
  fullName: function() {
    return this.firstName + "  " + this.lastName
  },
}

console.log(user.fullName()) // Akhila Ariyachandra 
```

Enter fullscreen mode Exit fullscreen mode

当在方法中访问`this`时，它指的是方法的所有者，也就是它所在的对象。

### “此”在一个内在函数中

```
const user = {
  firstName: "Akhila",
  lastName: "Ariyachandra",
  fullName: function() {
    return this.firstName + "  " + this.lastName
  },
  innerFunction: function() {
    function testFunction() {
      return this.firstName + "  " + this.lastName
    }

    return testFunction()
  },
}

console.log(user.innerFunction()) // undefined undefined 
```

Enter fullscreen mode Exit fullscreen mode

这里的`this`变化在`testFunction()`内部。为了理解为什么我们需要知道范围如何适用于`this`。

> 与变量不同，this 关键字没有作用域，嵌套函数不继承其调用方的 this 值。如果一个嵌套函数作为一个方法被调用，它的 this 值就是它被调用的对象。 <sup id="fnref2">[2](#fn2)</sup>

这意味着`testFunction()`的`this`不知道`user`对象，所以它为`this.firstName`和`this.lastName`返回`undefined`。

### 那么我们如何把适当的“这个”带入内在函数呢？

#### 在 ES5 之前

```
const user = {
  firstName: "Akhila",
  lastName: "Ariyachandra",
  innerFunction: function() {
    const that = this
    function testFunction() {
      return that.firstName + "  " + that.lastName
    }

    return testFunction()
  },
}

console.log(user.innerFunction()) // Akhila Ariyachandra 
```

Enter fullscreen mode Exit fullscreen mode

在 *ES5* 之前，没有合适的方法将`this`传递给内部函数。周围显示的示例是一种变通方法。 <sup id="fnref3">[3](#fn3)</sup>

### ES5

```
const user = {
  firstName: "Akhila",
  lastName: "Ariyachandra",
  innerFunction: function() {
    function testFunction() {
      return this.firstName + "  " + this.lastName
    }

    testFunction = testFunction.bind(this)

    return testFunction()
  },
}

console.log(user.innerFunction()) // Akhila Ariyachandra 
```

Enter fullscreen mode Exit fullscreen mode

*ES5* 引入了`bind`方法，允许我们设置函数的`this`。所以在我们运行完`testFunction = testFunction.bind(this)`这条线之后，`testFunction()`的`this`就变成了`user`对象。

### ES6+

```
const user = {
  firstName: "Akhila",
  lastName: "Ariyachandra",
  innerFunction: function() {
    const testFunction = () => {
      return this.firstName + "  " + this.lastName
    }

    return testFunction()
  },
}

console.log(user.innerFunction()) // Akhila Ariyachandra 
```

Enter fullscreen mode Exit fullscreen mode

*ES6* 为我们带来了**箭头函数**，它自动将`this`绑定到函数上，代码更少更干净。

## 那么 React 呢？

考虑下面的代码。

```
class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      text: "",
    }
  }

  setText() {
    this.setState({ text: "Hello World!" })
  }

  render() {
    return (
      <div>
        <h1>{this.state.text}</h1>
        <button onClick={this.setText}>Set Text</button>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们点击按钮，代码到达`this.setState({ text: "Hello World!" });`行，就会失败。为什么？因为到那时`this`将会是`undefined`。`this`没有隐式绑定到`setText()`，所以它默认为全局变量，在本例中是`undefined`，因为类运行在“严格模式”下。由于`this`没有被隐式绑定，我们需要将`this`显式绑定到`setText()`。

我们可以用之前 *ES5* 的例子来解决这个问题，但是这是非常不鼓励的。如果我们用`bind`来做，它会是这样的。

```
class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      text: "",
    }
    this.setText = this.setText.bind(this)
  }

  setText() {
    this.setState({ text: "Hello World!" })
  }

  render() {
    return (
      <div>
        <h1>{this.state.text}</h1>
        <button onClick={this.setText}>Set Text</button>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用`bind`，你必须在构造函数中完成。

箭头功能只需将`setText()`更改为箭头功能，就可以让事情变得更简单。

```
class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      text: "",
    }
  }

  setText = () => {
    this.setState({ text: "Hello World!" })
  }

  render() {
    return (
      <div>
        <h1>{this.state.text}</h1>
        <button onClick={this.setText}>Set Text</button>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 参考文献

* * *

1.  w3schools.com，[JavaScript 这个关键字](https://www.w3schools.com/js/js_this.asp)。 [↩](#fnref1)

2.  大卫·弗拉纳根，JavaScript:权威指南 [↩](#fnref2)

3.  amitai，[在 react 函数中使用“this”](https://gist.github.com/amitai10/adb66d6faa714e8c3cdb94946bb98356)[↩](#fnref3)