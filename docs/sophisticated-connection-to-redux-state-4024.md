# Redux 状态的复杂连接

> 原文：<https://dev.to/fosteman/sophisticated-connection-to-redux-state-4024>

*“简单是最复杂的”*——达芬奇

一个库 [react-redux](https://react-redux.js.org) 将 react 应用与 redux 状态管理耦合起来。

开发人员可以掌握以下高阶组件:

## `<Provider />`

```
import {Provider} from 'react-redux'

ReactDOM.render(
<Provider store={store}>
    <App />
</Provider>
document.getElementById('root')
); 
```

整个组件树中源于`App`种子的每个子组件都有一个对存储的隐式访问。因此，每个组件都能够分派动作并监听更新，以便重新呈现。但不是每个组件都必须监听更新。

这个组件的基本原则是 React ContextAPI 和 Provider 模式，我在以前的文章中已经描述过了。

## `connect`

有一个更高级的组件`connect`，将 redux 存储功能分派和状态传递给增强的组件。

```
import { connect } from 'react-redux'
function Component(props) { ...
}
const ConnectedComponent = connect(...)(Component); 
```

这个`connect` HOC 最多可以有四个参数作为配置:

`connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])(...);`

通常你只会用其中的两个:`mapStateToProps()`和`mapDispatchToProps()`。在不久的将来，我会写一些关于`mergeProps()`和`options`的文章。

**`mapStateToProps(state, [props]) => derivedProps` :** 可以传递给`connect` HOC 的函数。如果通过，connect HOC 的输入组件将从 Redux 存储订阅更新。因此，这意味着每次存储订阅注意到更新时，mapStateToProps()都将运行。`mapStateToProps()`函数本身在其函数签名中有两个参数:全局状态对象和可选的来自父组件的属性。该函数返回一个从全局状态派生的对象，也可以从父组件的属性派生。返回的对象将被合并到剩余的属性中，这些属性在使用时作为连接组件的输入。

**`mapDispatchToProps(dispatch, [props])`** :可以传递给`connect` HOC 的函数(或对象)。鉴于`mapStateToProps()`提供了对全局状态的访问，`mapDispatchToProps()`提供了对商店调度方法的访问。它使得分派动作成为可能，但是只传递简单的函数，这些函数将分派连接到更高级的函数中。毕竟，如果`connect`特设来改变状态，它使得将功能传递给输入组件成为可能。或者，您可以使用传入的属性将它们包装到分派的动作中。

那是一大块需要消化的知识。`mapStateToProps()`和`mapDispatchToProps()`这两个函数在开始时都可能会令人生畏。此外，它们还用于国外的高阶组件。但是，它们只允许您访问商店的状态和分派方法。

**序列中的概念**

```
View -> (mapDispatchToProps) -> Action -> Reducer(s) -> Store -> (mapStateToProps) -> View 
```