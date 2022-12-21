# # React 中的状态挂钩

> 原文：<https://dev.to/kritirai/state-hook-in-react-53c7>

一段时间以来，我们一直将函数组件称为*无状态*组件，每次我们需要使用本地状态时，都必须编写一个`class`。然而，随着 React 16.8 中钩子的引入，人们现在可以使用称为`useState`的内置钩子，或者称为*状态钩子*，允许人们向功能组件添加本地状态。

根据 [React.js 文档](https://reactjs.org/docs/hooks-state.html)，

> `State Hooks are basically functions that let you ‘hook into’ React state and lifecycle features from function components`

让我们看看如何使用状态钩子重写一个类组件。假设我们有一个`Like`组件，它呈现了总赞数以及一个 like 按钮和一个 like 按钮。当用户点击喜欢按钮时，喜欢增加 1，反之，当用户点击不喜欢按钮时，喜欢减少 1。

因为我们的组件需要记住喜欢的数量，以便能够更新和显示它，所以它需要利用*状态*。

在引入钩子之前，我们通常会写一个`class`来使用*状态*。

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

class Like extends Component {
  constructor(props) {
    super(props);
    this.state = { likes: 0 }
  }

  handleLike = (e) => {
    e.preventDefault();
    this.setState({ likes: this.state.likes + 1})
  }

  handleUnlike = (e) => {
    e.preventDefault();
    this.state.likes > 0 ? this.setState({ likes: this.state.likes - 1}): null;
  }

  render () {
    return (
      <div>
        <h4>Likes: { this.state.likes }</h4>
        <button style={{ backgroundColor: '#99ccff' }} onClick={ this.handleLike }> Like </button>
        <button style={{ backgroundColor: 'red' }} onClick={ this.handleUnlike }> Unlike </button>
      </div>

    )
  }
}

const el = <Like />

ReactDOM.render(el, document.getElementById('root')); 
```

这会给我们这样的结果:

[![gif](img/a42f96317659a06bdcfe7ad6562ff4ad.png)](https://i.giphy.com/media/JrYkYH2Jkyw58u9mJs/giphy.gif)

如果我们关注下面的代码片段，我们会看到我们用构造函数中的这一行`this.state = { likes: 0 }`将`likes`状态初始化为 0。

```
 constructor() {
    super();
    this.state = { likes: 0 }
  } 
```

现在，通过状态挂钩，我们可以使用`useState`重写上面的代码。

```
import React, { useState } from 'react';
import ReactDOM from 'react-dom';

function Like() {
 const [likes, setLikes] = useState(0);
 ... 
```

## 这里发生了什么？

首先，我们从 React 导入了`useState`。然后，我们将类组件转换为函数组件`Like()`。最后，在函数内部我们有这样一个 liner:

`const [likes, setLikes] = useState(0);`

`useState`返回一对值——当前状态和更新它的函数。因此，使用[数组析构方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Array_destructuring)，我们声明并赋值给一个状态变量`likes`和一个函数`setLikes`，这类似于`class`中的`setState()`方法。你还可以看到`useState()`接受了一个参数，这是组件的初始状态，在这种情况下是`0`，因为我们还没有得到任何人的喜欢:(

## 更新状态

既然我们已经使用了上面声明的`setLikes`函数，我们现在可以直接调用该函数来更新状态。让我们重写我们的处理函数`handleLike`和`handleUnlike`。

```
 const handleLike = (e) => {
    e.preventDefault();
    setLikes(likes + 1)
  }

  const handleUnlike = (e) => {
    e.preventDefault();
    likes > 0 ? setLikes(likes - 1): null;
  } 
```

看，我们如何轻松地调用`setLikes`来更新我们的`likes`？因此，我们可以只写`setLikes(likes + 1)`，而不是像在`class`中那样写`this.setState({ likes: this.state.likes + 1})`。

让我们通过分别用`{ handleLike }`和`{ handleUnlike }`替换`{ this.handleLike }`和`{ this.handleUnlike }`来更新函数的`return`值。最后，这里是我们使用状态钩子重写的`Like`组件。

```
import React, { useState } from 'react';
import ReactDOM from 'react-dom';

function Like() {
  const [likes, setLikes] = useState(0);

  const handleUpClick = (e) => {
    e.preventDefault();
    setLikes(likes + 1)
  }

  const handleDownClick = (e) => {
    e.preventDefault();
    likes > 0 ? setLikes(likes - 1): null;
  }

  return (
    <div>
      <h4>Likes: { likes }</h4>
      <button style={{ backgroundColor: '#99ccff' }} onClick={ handleUpClick }> Like </button>
      <button style={{ backgroundColor: 'red' }} onClick={ handleDownClick }> Unlike </button>
    </div>
  )
}

const el = <Like />

ReactDOM.render(el, document.getElementById('root')); 
```

所以，给你！有了 React 钩子，函数组件现在可以有一些状态，而不必编写那些笨重的类。然而，这并不意味着您必须返回并转换所有现有的类组件。此外，钩子是完全可选的，它们没有取代类的意图。然而，从现在开始，如果您需要在函数组件中使用*状态*，您至少可以选择使用钩子。注意，React 16.8 附带了钩子，所以如果你想使用它们，一定要升级 React 和 ReactDOM。