# JSX 独一无二的钥匙道具。

> 原文：<https://dev.to/keevcodes/the-unique-key-prop-in-jsx-4bf3>

# 概述

所以我写这篇文章其实很尴尬，因为我喜欢认为自己对 React 有很好的理解，然而几乎每隔一天，我都被琼恩·雪诺时刻所折服。最近，当被问及为什么我们需要在 React 中为动态呈现的内容使用键时，我被难住了。我在 React 中遇到过很多次这种错误，通常只是简单地将项目的索引作为 key prop 传递，然后就忘记了。现在，大多数时候你的组件会表现得很好，但有几个实例可能会影响你的 JSX，或者至少让你像我一样，为不知道一个相当简单的问题的答案而付出代价。

[![Jon Snow knows nothing meme](img/662de0f2d43c27c6fae63bd51f9d4e39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BaO0BhYO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/17259420/60866511-d2c49180-a228-11e9-8b2b-d25a65f396d3.jpg)

# 交易密钥是什么？

React 可以快速更新视图的原因之一是，它只更新 DOM 中需要更新的部分。例如，假设我们有一个简单的待办事项列表，用户可以简单地添加一个新的待办事项。

```
const TODOS = ["get groceries", "fold laundry", "Feed Fido"];

const TodoList = props => {
  return (
    <ul className="Todo">
      { TODOS.map(todo => <li>todo</li>)  }
    </ul>
  )
}; 
```

Enter fullscreen mode Exit fullscreen mode

React 更新我们的 todo 列表的最快和最简单的方法是，我们将所有当前项目保持相同的顺序，并在列表的末尾追加新的 todo。React 可以很容易地匹配我们的原始条目，因为这些条目都没有改变，React 需要做的唯一更新是对我们新创建的 todo 条目进行更新。然而，如果我们将新的项目添加到列表的顶部，React 就不再能容易地匹配我们的原始列表，必须更新列表中的每个项目。现在，一些项目不会影响我们应用程序的性能，但是随着列表的增长，这些性能变化变得非常明显。

如果我们希望我们的用户能够按照重要性移动任务，甚至在任务完成后删除项目，我们如何处理这个问题并优化性能呢？嗯，你可能已经猜到了，这正是 prop 试图解决的问题。

```
const TODOS = ["get groceries", "fold laundry", "Feed Fido"];
let id = 1;

const TodoList = props => {
  return (
    <ul className="Todo">
      { TODOS.map(todo => <li key={id++}>todo</li>)  }
    </ul>
  )
}; 
```

Enter fullscreen mode Exit fullscreen mode

为我们的每个 Todo 项目分配一个唯一的键，React 就可以将我们的项目与它们的键相匹配，因此，即使我们的用户按照重要性、最近创建的或其他模式对列表进行排序，React 也会知道需要创建或更新哪些项目，以及可以忽略哪些项目。

但是请注意，我在这里没有使用该项的索引。在写这篇文章的时候，我发现了另一个有用的花絮；当没有提供条目索引时，React 默认使用条目索引作为键。只要列表顺序不变，索引通常是好的，但是如果我们重新排序列表，我们可能会得到一些意外的状态错误。查看关于[对账](https://reactjs.org/docs/reconciliation.html#recursing-on-children)的 React 文档，了解更多关于这种行为的信息。

# 结论

实际上已经有相当多的有用资源解释了这一点，但我认为另一个资源不会有什么坏处。对我来说(希望对你也是)，最大的收获是我倾向于简单地修复我在代码中得到的任何控制台错误，以便我可以继续制作组件并获得我的视图。但是忽略这些错误可能会导致应用程序的渲染时间变慢或出现意外行为。此外，我需要把我的骄傲留在门外，因为我学得越多，我就越意识到我实际上知道的是多么少。

我很想知道你是否有过类似的经历，忽略了一些框架、语言或任何其他主题的简单概念，这是我们广大开发者的世界。