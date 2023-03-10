# 根据这两条规则正确使用 React 钩子

> 原文：<https://dev.to/chrisachard/use-react-hooks-correctly-with-these-two-rules-52le>

钩子是在 React Conf 2018 上引入的，作为向功能性 reactor 组件引入状态的一种简单方法，它一直在稳步普及。

需要注意的是，如果你不想使用钩子，你可以不使用钩子，但是如果你要使用，请确保你遵守了钩子的两条规则！否则，不好的事情可能会发生。

# 钩子为什么被创造出来

首先，我们来看看钩子要解决的问题。

这里有一个 React 类组件，它有一个名为`count`的状态位，当按钮被点击时，它开始计数:

```
// OLD WAY: Class Components

class Counter extends React.Component {

  state = {
    count: 0
  }

  render() {
    return (
      <div>
        <button 
          onClick={() => 
            this.setState({ count: this.state.count + 1 })
          }
        >
          +1
        </button>
        <p>Count: {this.state.count}</p>
      </div>
    )
  }
} 
```

这很好，但是有几个部分很容易产生错误。

## 问题 1:`this`是什么意思？

对于新开发人员来说，JavaScript 最令人困惑的部分之一是`this`关键字会根据上下文改变含义。这包括非常重要的，但是(看起来)任意的定义函数的方式。

比如这个函数:

```
// probably not what you want
function onClick() {
  this.setState({ this.state.count: count + 1 })
} 
```

没有将`this`绑定到类组件——因此可能不会像您想要的那样运行！相反，您必须记住要么将该函数绑定到类，要么使用箭头函数:

```
// probably what you want
const onClick = () => {
  this.setState({ this.state.count: count + 1 })
} 
```

直到你对类组件的各个部分中的`this`的含义有了一个非常好的感觉，它可能会导致微妙的、非常令人困惑的错误。

**钩子通过完全消除跟踪`this`含义的需要，简化了**。这是因为没有您必须引用的类(因为所有东西都是带钩子的功能组件)。

## 问题二:使用`this.state...`访问数据并调用`this.setState`进行更改

事实上,`state`存在于类组件中，这意味着任何时候你想访问 state 中的一个值，你必须在它前面加上`this.state`。这对初学者来说可能会很困惑，但对有经验的程序员来说也是如此。

为了证明它有多烦人——在为本文创建演示时，我最初键入了下面的 onClick 函数:

```
// Can you spot the bug?
...
  this.setState({ count: count + 1 })
... 
```

你看到虫子了吗？没错。在`setState`通话中，我忘记说`this.state.count:`，而不是只说`count:`。这没有导致渲染错误或任何事情-但它就是不工作；我花了一些调试来找出哪里出错了...*烦人！*

**钩子通过移除类`state`的概念简化了**，并且直接给出对值和集合函数的访问。不再有`this.state`！

## 问题三:人们越来越多地使用功能组件

由于功能组件是*‘只是功能’*，并且通常更容易键入和推理，越来越多的人默认使用功能组件而不是类组件。

问题是，一旦你想给一个功能组件添加状态，你就必须把它转换成一个类组件，或者引入一个更复杂的库，比如 Redux。

**钩子通过给你一种直接用`useState`钩子给功能组件添加状态的方法来简化这个**。

然后——用`useEffect`钩子，你可以复制类组件的生命周期方法，突然——*你不再需要类了！*

所以你现在可以在任何地方继续使用你的功能组件。

# 钩子如何工作

好的——让我们来看看同一个`Counter`例子；不过这次用的是钩子:

```
// NEW WAY: Hooks
import React, { useState } from "react";
...
const Counter = () => {
  const [count, setCount] = useState(0)

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        +1
      </button>
      <p>Count: {count}</p>
    </div>
  )
} 
```

我们没有用`state = {}`设置初始状态，而是使用`useState`钩子定义一个新的状态位，我们称之为`count`，默认为`0`。

重要的是(对于后面的规则)- React 将这个值**存储在一个数组**中，该数组包含了这个函数中用钩子创建的所有值。我们这里只有一个；但是让我们再添加一个来看看是什么样子的:

```
// Multiple states

const Counter = () => {
  const [count, setCount] = useState(0)
  const [name, setName] = useState('Chris')
  ...
} 
```

所以通过上面的两个`useState`调用，我们知道 React 存储的是两个值的数组。

好吧！现在我们已经获得了理解钩子规则所需的所有信息。

# 钩子的规则

## 规则 1:在你的组件顶层无条件调用钩子

 *因为钩子存储在一个数组中，所以它们被调用的顺序很重要。由于 React 组件在每次数据改变时都会被重新渲染，这意味着在每次渲染时必须以**完全相同的顺序**调用**完全相同的钩子**。

这意味着如果你在这里添加了一个`if`语句:

```
// DON'T DO THIS!
...
if(myBool) {
  const [count, setCount] = useState(0)
}
const [name, setName] = useState('Chris')
... 
```

状态有时会被创建，有时不会。但是 React 不能在内部跟踪它——所以它会弄乱存储在该组件数组中的所有状态的值。

还有——不要把钩子放在循环里(或者任何其他控制功能):

```
// ALSO DON'T DO THIS!
...
for(my conditions) {
  ...useState...
  ...useEffect...
}
... 
```

...因为如果你的条件是基于变化的变量，那么你会让钩子在不同的渲染中以不同的顺序运行。

## 规则二:只调用 React 函数中的钩子或者自定义钩子

该规则存在的原因与规则 1 相同，但略有不同。

例如，如果我们创建了一个名为`doSomething` :
的助手函数

```
// DON'T DO THIS

function doSomething() {
  const [count, setCount] = useState(0)
  ... do something with count ...
} 
```

那么你(或者另一个开发人员)可能没有意识到那个`doSomething`函数实际上调用了一个钩子——并且可能会不按顺序调用`doSomething`:

```
// MISUSE of doSomething

const Counter = () => {
  return <button onClick={doSomething}>Do it!</button>
} 
```

这样钩子就断了。

它破坏了它们，因为 react 内部无法跟踪乱序运行的钩子(与规则#1 相同)——所以**只在 React 组件的顶部使用钩子，或者在您创建的自定义钩子中使用**。

# 钩子并不可怕

钩子解决了开发人员在使用 React 类组件时遇到的一些常见问题。你不一定要使用它们(所以不要仅仅因为''就去替换你的一堆代码*)——但是如果你要用，那么就要遵守规则:*

1.  **在组件的顶层无条件调用钩子**
2.  **只调用 React 函数中的钩子或者自定义钩子**

就是这样！

一旦你知道 React 把钩子值保存在数组中，那么它就有意义了:不要改变钩子被调用的顺序，否则 React 就不能跟踪什么是什么！

这篇文章最初发表在:[https://chrisachard . com/use-react-hooks-rightly-with-this-two-rules](https://chrisachard.com/use-react-hooks-correctly-with-these-two-rules)*