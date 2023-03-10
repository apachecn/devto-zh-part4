# 在不同的框架中安装 React 组件

> 原文：<https://dev.to/eljenso/react-component-mounting-in-a-different-framework-10mm>

我们正在进行的项目最初是一个 [Backbone.js](https://backbonejs.org/) 项目，但是我们现在开始将 React 集成到其中。
这篇文章不是关于背后的推理，而是关于一些不同的东西:
我们如何在主干应用程序中使用(或安装)React 组件。

当我们从头开始编写一个新的 React 应用程序时，我们定义我们的 entrypoint 组件，通常称为`App`，并通过 ReactDOM 将它挂载到现有 DOM 中的某个位置:
`ReactDOM.render(<App />, document.getElementById("root"));`。
然后我们将开始开发应用程序，它完全驻留在那个`App`组件中。

但是，当我们有一个用另一个框架(在我们的案例中是主干)编写的现有应用程序，我们现在想在其中使用 React 时，情况就不一样了。我们的选择是:

1.  从头开始重写整个应用程序
2.  用 React 实现新特性，在过程中慢慢用 React 代码替代 Backbone.js 代码

出于许多原因(可能会在以后的帖子中讨论)，我们选择了选项 2。

* * *

让我们定义一个我们想要集成到现有应用程序中的新组件:

```
function CounterButton() {
  // Define state using hooks
  const [count, setCount] = React.useState<number>(0);

  // Return button displaying current state and incrementing state on click
  return (
    <button onClick={
      () => setCount(count + 1)
    }>
      {count}
    </button>
  )
} 
```

组件呈现一个按钮，显示用户点击它的频率。
该组件有一个状态`count`，初始设置为`0`，以及相应的设置器功能`setCount`。

现在，为了在某处将`CounterButton`添加到我们现有的应用程序中，我们使用`ReactDOM.render`将其渲染到现有的 DOM 元素:
`ReactDOM.render(<CounterButton />, document.getElementById("someElement"));`。

我们完了。

* * *

我们大概是这样认为的。

如果您希望以后在相同的地方重用相同的组件，该怎么办？例如一个模态(也称为对话)，用户在某一点关闭，但最终可能会再次打开。

让我们给`CounterButton`组件添加一个`show`状态，可以让`<button>`消失:

```
function CounterButton() {
  // Define state using hooks
  const [count, setCount] = React.useState(0);
  const [show, setShow] = React.useState(true);

  // Return button displaying current state and incrementing state on click
  if (!show) {
    return null;
  }
  return (
    <button onClick={
      () => {
        if (count === 5) {
          setShow(false);
        }
        setCount(count + 1);
      }
    }>
      {count}
    </button>
  )
} 
```

如果`!show`为真，那么`CounterButton`将返回`null`，当`show`状态从`true`变为`false`时，从 DOM 中完全删除`<button>`。
在这里，这是用户点击按钮时`count`为`5`的情况。

这个逻辑是我们目前用来关闭一个模态的。
当用户触发该模态的关闭逻辑时，我们将`show`状态设置为`false`，这将导致该模态从 DOM 中移除..

但是如果你想在它消失后再次显示`CounterButton`呢？
简单地再次执行下面的调用，对吗？
`ReactDOM.render(<CounterButton />, document.getElementById("someElement"));`
遗憾的是，`CounterButton`不会露面。

从[反应文件](https://reactjs.org/docs/react-dom.html#render):

> 如果 React 元素以前已经呈现在 container 中，那么这会对它执行更新，并且只在必要时改变 DOM 以反映最新的 React 元素。

换句话说，ReactDOM 将像以前一样渲染*实例*，只是使用了更新的道具。
React 会使用之前用过的`CounterButton`的实例，状态不变:`show`还是`false`。

我们解决这个问题的第一个想法是每次在传递给`ReactDOM.render`之前创建一个新的`CounterButton`实例。
为此，我们将`CounterButton`函数的主体封装在一个箭头函数中，本质上是一个匿名的功能组件。`CounterButton`现在将返回这个匿名功能组件:

```
function CounterButton() {
  return () => {
    // Define state using hooks
    const [count, setCount] = React.useState(0);
    const [show, setShow] = React.useState(true);

    // Return button displaying current state and incrementing state on click
    if (!show) {
      return null;
    }
    return (
      <button onClick={
        () => {
          if (count === 5) {
            setShow(false);
          }
          setCount(count + 1);
        }
      }>
        {count}
      </button>
    )
  }
}

// Create new functional component to pass into ReactDOM.render
const CounterButtonInstance = CounterButton();
ReactDOM.render(<CounterButtonInstance  />, document.getElementById("root")); 
```

无论我们用返回`CounterButton()`到`document.getElementById("root")`的方式调用`ReactDOM.render`多少次，`ReactDOM.render`总是将这个匿名的功能组件视为与之前的组件不同的组件。
那是因为它*是*一个不同的匿名功能组件。

但是这种方法至少有一个问题:
`CounterButton`不再是功能组件，而是返回功能组件的函数。
这使得在 React 应用程序中重用`CounterButton`变得不可能。

现在，对于我们当前的解决方案，我们删除了上一个代码片段中引入的封装。
相反，我们利用特殊组件道具`key`，阅读更多关于它的[反应文档](https://reactjs.org/docs/lists-and-keys.html) :

```
ReactDOM.render(
  <CounterButton key={new Date().getTime()} />, document.getElementById("root") ); 
```

我们在这里利用了`key`属性的一个重要属性:如果 React 将要重新渲染一个组件，而这个组件的`key` *在上次渲染后*发生了变化，React 将会丢弃之前的版本并从头开始渲染。
我们使用当前时间(以毫秒为单位)作为该属性的值；由于这将在渲染之间改变，React 将用一个新的状态创建一个新的`CounterButton`实例！🎉

下面你可以看到一个展示这种方法的代码笔。
按几下那个按钮，它就会消失，再也不会回来。
但是如果你取消那些关键道具的注释，`CounterButton`将每 2 秒重置一次。

[https://codepen.io/eljenso/embed/WqZzQq?height=600&default-tab=js,result&embed-version=2](https://codepen.io/eljenso/embed/WqZzQq?height=600&default-tab=js,result&embed-version=2)

* * *

*一些事后的想法*

对于匿名函数组件，我们还可以引入另一个函数，它返回一个匿名函数，返回原始的`CounterButton` :

```
function CreateCounterButton() {
  return () => CounterButton()
} 
```

调用`CreateCounterButton`将在每次调用时创建一个新的`CounterButton`实例。这将保持我们的`CounterButton`可重复使用。

上面描述的任何方法都有一个缺点:
`CounterButton`仍然是 ReactDOM 的一部分，即使在它从 DOM 中移除之后。
我们应该确保`CounterButton`一旦不再被使用，就正确地从 ReactDOM 中卸载；否则，可以认为是内存泄漏，这会导致性能问题。
ReactDOM 提供了一个`unmountComponentAtNode(container)`方法，允许卸载任何安装在`container`中的 React 组件。

在我们的例子中，我们将这样使用它:

```
ReactDOM.unmountComponentAtNode(document.getElementById("root")) 
```

但是由于`CounterButton`不知道，也不应该知道它必须以这种方式卸载，所以应该从外部处理这个调用。

我们还没有进一步研究如何使用`unmountComponentAtNode`。
由于我们还没有很多 React 组件(我们目前在代码库中有大约 40 个 tsx 文件)，所以`key` prop 方法似乎已经足够了。一旦认为将未使用的组件留在 ReactDOM 中会影响应用程序的性能，我们应该进一步研究这种方法。