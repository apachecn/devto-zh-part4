# React 钩子:使功能组件有状态

> 原文：<https://dev.to/guin/react-hooks-making-function-components-stateful-19lj>

React 挂钩允许我们在功能组件中使用(“挂钩”)React 特性，比如状态和生命周期方法。在 hooks API 被引入 React 之前，如果我们想让一个组件保持某种状态，我们就必须把它写成一个类组件。

虽然学习你所使用的语言中的事物为什么以及如何工作是很重要的，但是类最初会对最初的生产力或者对其他概念的理解造成不必要的障碍。例如，作为一名新的 react 开发人员，您可能已经了解了状态和事件在 react 中是如何工作的，但是由于您对 [`this`关键字是如何工作的还没有一个坚实的理解，您可能会被一个 bug 绊倒。](https://dev.to/guin/tired-of-guessing-what-this-is-referring-to-3nfk)

在这篇文章中，我们将看看 useState 钩子是如何让我们的函数组件有状态的。

## 使用 useState 钩子

为了使用 useState 钩子，我们首先需要导入它。在我们导入它之后，我们可以用它在一个函数组件中创建一些本地状态。

```
import React, { useState } from 'react';

function Example() {
...
} 
```

`useState`接受一个参数并返回一对值。这对值中的第一个是当前状态值，第二个是允许您更新该值的函数。React 在渲染之间保留这种状态。

在下面的例子中，`useState`返回一个状态变量`category`和一个函数`setCategory`，我们可以用它来更新类别变量的值。传入的参数是您希望的初始状态，并且将仅用于初始渲染。在这个例子中，我们将字符串`"Clothing"`作为参数传入。

```
import React, { useState } from 'react';

function Example() {
  const [category, setCategory] = useState("Clothing")
  return (
    <p>{category}</p>
  )
} 
```

不像在类组件中，当我们使用`useState`时，作为参数传入的值不一定是对象。我们可以传入一个字符串，一个数字，一个对象，空值等等。我们也可以多次使用`useState`来创建不同的状态变量。由`useState`返回的函数在概念上类似于在类组件中调用`this.setState`。但是，与类中的 this.setState 不同，更新状态变量总是替换它，而不是合并它。

### 所以我们创建了一些局部状态，我们如何在代码中使用和更新它呢？

在下面的例子中，我们有一个函数组件`ProductListing`，它有两个对 useState 的调用。由于 useState 返回对中的第一项只是一个变量，我们将初始值作为参数提供，所以我们可以在组件 jsx 中直接使用这个变量，而不必像在类组件中那样编写类似于`this.state.category`的东西。

```
import React, { useState } from 'react';

function ProductListing() {
  const [category, setCategory] = useState("Clothing")
  const [product, setProduct] = useState({"id" : 1, "name": "jeans"});

  return (
    <div>
      <h1>{category}</h1>
      <p>{product.name}</p>
    </div>
  );
} 
```

让我们添加一个输入，允许我们键入一个类别并更新我们的类别状态变量。我们的输入有一个事件处理程序`handleCategoryChange`，它使用`setCategory`函数用输入的当前值更新类别状态变量。当我们输入时，我们会看到类别被实时更新。

```
import React, { useState } from 'react';

function ProductListing() {
  const [category, setCategory] = useState("Clothing")
  const [product, setProduct] = useState({"id" : 1, "name": "jeans"});

  const handleCategoryChange = (e) => {
    setCategory(e.target.value)
  }

  return (
    <div>
      <input onChange={handleCategoryChange}/>       <h1>{category}</h1>
      <p>{product.name}</p>
    </div>
  );
} 
```

然而，我想重申，更新一个状态变量总是替换它，而不是像类中的`this.setState`那样合并它。如果我们想更新我们的产品状态变量，并用不同的产品对象调用 setProduct，我们将替换整个产品对象，而不是将其与前一个合并。这是为什么我们可以有很多不同的状态变量的很大一部分原因。建议根据哪些值会一起变化，将状态拆分为多个状态变量。你可以在官方的 react 文档[这里](https://reactjs.org/docs/hooks-faq.html#should-i-use-one-or-many-state-variables)阅读更多关于思考这个问题的方法。

希望您已经更好地理解了 useState 钩子如何允许您向函数组件添加本地状态！我建议通读一下 [useState 挂钩](https://reactjs.org/docs/hooks-state.html)上的 react 文档，以便更深入地了解！

如果您有任何问题、意见或反馈，请告诉我。关注每周关于 JavaScript、React、Python 和 Django 的新帖子！