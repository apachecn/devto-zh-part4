# 用反应钩子思考

> 原文：<https://dev.to/dfmartin/thinking-in-react-hooks-1cc1>

如果你还没有，请阅读 React Hooks 上的[官方文档。我在这里提出的是我对钩子的看法，我发现这是一个有用的解释。](https://reactjs.org/docs/hooks-intro.html)

## 思考在钩子上

React 中的钩子提供了一种思考组件如何工作的新方法。对于类组件，我们习惯于按照组件的生命周期来思考。组件正在安装吗？已经挂载了吗？它正在更新吗？它要离开我们了吗？然而，钩子倾向于 React 将组件与其状态同步的想法。

我发现，一旦我开始把 React 更多地看作是类固醇的模板引擎，这就更容易理解了。让我把这个抽出来:

有些人可能认为 JSX 是将 HTML 引入 JavaScript 的一次尝试，但我相信这只是因为 JSX 看起来与 HTML 很相似。相反，把 JSX 当作一个模板。你告诉 React 这是你希望你的 UI 组合在一起的方式，或者你希望它看起来是什么样子。React 获取这个“模板”,并在反应器中运行它，然后输出 DOM 元素。

在这个过程中，你的状态可能会在某个时候出现，并且可能会随着时间而改变。

组件的整体是提供和管理状态，并使用该状态指定您希望组件看起来是什么样子。

钩子。。。

有了 React 钩子，我们可以从状态变化的角度来思考，结果是，UI 应该如何受到这些变化的影响。

### 使用状态

是最基本的挂钩。它为状态提供了一个超级简单的机制:状态本身，以及改变它的方法。

`const [state, setState] = React.useState('')`

通过这段代码，您可以在整个组件中使用`state`变量，并使用`setState`函数来更改它。React 将负责在渲染调用之间保持状态值。您提供给`useState`调用的参数只是初始值。如果使用 TypeScript，将会推断出`state`变量的类型。你也可以通过泛型明确声明类型:`const [state, setState] = React.useState<string | null>(null)`。

### 使用效果

是下一个你可能最常使用的钩子。很多时候你只需要两个钩子`useState`和`useEffect`。

`useEffect`需要两个参数。一个函数和一个数组。

先从数组说起。数组是一个事物的列表，当这些事物被改变时，将导致效果运行。如果他们不改变，效果就不会运行。这些东西是你的组件的变量。它们可能是国家的一部分或道具，

功能是效果真正存在的地方。当数组中的任何内容发生变化时，将执行该函数。

首先要注意的是，效果总是在启动时运行一次。这不是可选的。它是必需的，因为 React 将在第一次运行效果时做一些额外的特殊事情——它将捕获它的返回值。

这是需要注意的第二件重要的事情。你为效果提供的函数可以返回某个东西，而且那个东西很重要。您返回的可能是以下两种情况之一:未定义的或另一个函数。React 会用这个来*清理*你的效果。这个需求有一个副作用:你的效果可能不是一个异步函数。为什么？异步函数返回承诺，效果只能返回未定义的或一个函数。我将在后面介绍解决这个问题的方法。

回到阵列中。把这个数组想象成问题*“这个效果应该在什么时候运行？”的答案会很有帮助*而且有三种可能的答案:一次且只有一次，永远，视情况而定。

*   运行一次:给它一个空列表。`[]`
*   永远运行:不要给它列表。
*   视情况而定:给它一个会导致它改变的事情列表

```
React.useEffect(() => {
    console.log('this effect will only run once')

    // and it does nothing to clean up
}, [])

React.useEffect(() => {
    console.log('this effect will run every render')

    return () => {
        console.log('this is run when this effect is cleaned up')
    }
})

React.useEffect(() => {
    console.log('this effect will run the first render, and then only when the first name changes')
}, [firstName]) 
```