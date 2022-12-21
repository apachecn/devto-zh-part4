# React 挂钩改变了我对功能组件的看法

> 原文：<https://dev.to/jenshaw/react-hooks-changes-my-mind-about-functional-components-3k39>

在我和我的项目伙伴将 React Bootstrap 集成到我们的 React 项目中之后，本周我得到了一个关于 hooks 的惊喜介绍，尽管我们使用 React 才几个星期，但这种体验实际上并不太糟糕！

* * *

### 什么是钩子？

“钩子”是让你从功能组件中“钩入”反应状态和生命周期特征的功能。“钩子在类内不起作用——它们让你不用类就能使用 React，” - [React Docs](https://reactjs.org/docs/hooks-overview.html#but-what-is-a-hook)

钩子太棒了。到目前为止，我只学会了如何使用一个钩子，`useState`(还有`useEffect`和`useContext`)，但是我发现这是一个很好的解决方法，可以解决我最讨厌的 React 问题之一，即必须在使用类和函数组件之间做出选择。

* * *

### 类组件是我的安全空间

在我和我的团队学习如何使用 React 的前几周，我主要关注如何微调基本类组件的结构。

我学会了如何使用生命周期方法，如`constructor`创建状态和`render`显示 HTML 元素，如何创建事件处理程序来处理事件侦听器，如何使用`this.state.something`引用和使用状态值，以及如何使用`setState()`设置状态。

如果我想创建一个类组件来显示一个全功能的文本输入，其中可以键入文本，输入的数据可以保存在某个地方供以后使用，有几件事我必须首先考虑。我需要:

*   在我的构造函数中创建状态，
*   呈现输入标记并将其值赋给其状态值，以及
*   添加事件处理程序，在数据发生更改时接收数据，然后在每次更改时重置或更新状态值。

React 代码应该是这样的:

```
import React, { Component } from "react";

export default class StandardReactForm extends Component {

  // Create state using constructor boilerplate
  constructor() {
      super();
      this.state = {
          name: "Peanut"
      }
  }

  // Create event handlers to accept input values and update state
  handleNameChange = (ev) => {
    this.setState({
        name: ev.target.value
    })
  }

  //render input tag
  render() {
      return (
        <div>
          <form>
          <label>Name:
            <input
              type="text"
              name="name"
              // set current value of input to current state value
              value={this.state.name}
              // refer to event handler on input change
              onChange={this.handleNameChange}
            />
          </label>
        </form>
      </div>
    );
  }
}; 
```

* * *

### 功能组件和我为什么从来不喜欢它们

就在我开始掌握类组件的时候，我的老师介绍并偶尔鼓励我使用功能组件，这些组件的结构在几个方面有很大的不同。由于种种原因，我很难对他们产生好感。

尽管 functional 将道具作为参数(这很方便...我*猜*...)，这是一些额外的记忆。在一个类组件中，你用`this.props.data`来引用 props，而在一个函数组件中，你只需要用`props.data`来引用它。如果说功能组件节省了我输入额外的`this`的时间和精力，那么当我在使用类和功能组件之间工作时，我也会浪费时间错误地输入对 props 的引用。

此外，与在类组件中可以定义方法不同，您必须在功能组件中使用`const`来声明函数。这又一次让我容易犯更多的错误，因为这是我需要记住的另一件事...如果我还记得我是在一个功能组件中编码的话。

最后，在功能组件中，您不能创建或设置状态或使用生命周期方法。我不认为这会对我已经构造的组件产生巨大的影响，因为如果我不需要创建状态或更新它，我会简单地选择在我的类组件中省略构造函数。

* * *

当我可以总是可靠地使用类组件时，使用功能组件和类组件似乎没有足够的好处。我欣赏一致性，如果可能的话，我宁愿坚持单一类型，而不是来回切换，不断犯错。

* * *

### 随着一声单钩，类部件被敲出环...而功能组件胜出！

当终于到了研究和更好地理解钩子的时候，我发现它们并不像我预期的那样复杂。像所有功能更新一样，它们旨在让您的生活更加轻松简单！

例如，`useState`是建立地方政府的一种非常干净的方式。只需一行代码，您就可以为您想要跟踪的状态声明一个变量名，一个在事件处理程序中设置状态的函数，并初始化状态值，所有这些都在*功能组件*中完成！

你自己看看吧！那个钩子看起来很光滑！

```
import React, { useState } from "react";

// functional component
const HooksForm = props => {

  // create state property 'name' and initialize it 
  const [name, setName] = useState("Peanut");
  const handleNameChange = e => setName(e.target.value);

  return (
    <div>
      <form>
        <label>Name:
          <input
            type="text"
            name="name"
            // set current value of input to current state value
            value={name}
            // refer to event handler on input change
            onChange={handleNameChange}
          />
        </label>
      </form>
    </div>
  );
};

export default HooksForm; 
```

* * *

在这里使用`useState()`有很多好处。

*   我们可以避免使用`constructor`来创建状态。
*   我们可以一口气完成两项任务。这里我们定义了一个变量， *"name"* ，带有一个初始状态值 *"Peanut"* ，以及一个特定的函数 *"setName"* ，其唯一的功能就是更新它。
*   这使我们不必写这么多行代码。它很简洁，看起来更干净，更容易阅读。
*   我们可以通过名字来引用状态属性或它的集合函数，只要我们需要它，就用它的名字。不再链接`this`或`this.state`。

最重要的是，(这也是我最喜欢这个方法的部分)我们再也不用为是否应该使用类或函数组件而苦恼了。我很高兴知道我可以只选择一个，坚持下去，并相信它会做我想要它做的事情。

* * *

*关于钩子的更多信息，请阅读 [React Docs](https://reactjs.org/docs/hooks-intro.html) 的优秀文档