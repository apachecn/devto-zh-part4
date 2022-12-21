# 面向简单应用的 React 状态管理库

> 原文：<https://dev.to/hitochan777/react-state-management-library-for-simple-apps-2oim>

我目前正在用 Next.js 开发一个简单的 web 应用程序，虽然它很简单，但是我想在全局范围内使用一些状态。
目前有几种管理全局状态的方法。

1.  还原或 Mobx
2.  使用 React 上下文将`state`和`setState`从`useState`传递给子组件
3.  使用 react-first 全局状态管理库(例如， [reactn](https://github.com/CharlesStover/reactn) )

谈到 React 中的全局状态管理，Redux 是使用最广泛的库之一。然而，这对于我的简单用例来说太多了，因为您必须用动作类型和有效负载来定义动作创建者。

使用 React 上下文或全局状态管理库似乎简单得多。但是我对这些方法感到笨拙的一点是，你将一个(部分)状态对象传递给`setState`或者你使用的任何状态设置函数。这意味着调用者必须知道状态对象的内部结构。通常这些功能被称为内部组件。但是我不认为组件应该知道内部。
如果一个组件可以用一些参数调用一个方法来更新一个全局状态而不知道细节，那不是很好吗？
在名为`hyperapp`、[的微框架中，你可以用动作](https://github.com/jorgebucaran/hyperapp#dispatching-actions)来更新状态。
一个动作采取
的形式

```
const someAction = (prevState, arg1, ..., argN) => {/* return newState */} 
```

然而，据我所知，我找不到任何做类似 hyperapp 的库。所以我创造了 [`react-state-action-hooks`](https://github.com/hitochan777/react-state-action-hooks) (我知道...这个名字有点长...)

## 快速介绍

首先，定义初始状态和动作定义。
动作定义中的每个键映射到一个动作定义。
动作定义是一个有点类似于 hyperapp 动作的函数，只是它是一个返回另一个函数的函数。
外部函数的参数是从相应的动作定义生成的动作的参数。
返回函数的参数是之前的状态和动作。
如下例所示，你可以从先前的状态返回一个新的状态，也可以调用其他动作。

```
const initialState = {
  count: 0,
};

const actionDefs = {
  incrementBy: (delta) => (state) => ({
    ...state,
    count: state.count + delta,
  }),
  decrementBy: (delta) => (state, actions) => {
    actions.incrementBy(-1);
  },
}; 
```

准备好初始状态和动作定义后，您可以将它们传递给 useActionState 以获得`state`和`actions`！
所有的动作定义都变成了动作，而且它们中的每一个都是一个函数(不是返回函数的函数)。您可以在动作定义中使用外部函数的参数来调用动作。
这样，你就不必知道国家的内部结构了。

```
const Counter = () => {
  const { state, actions } = useActionState(
    initialState,
    actionDefs
  );
  return (
   <div>
      <span>{state.count}</span>
      <button onClick={() => {actions.incrementBy(1);}}>
        +1
      </button>
      <button onClick={() => {actions.decrementBy(1);}}>
        -1
      </button>
    </div>
  );
}; 
```

在上面的例子中，钩子是在一个组件中被调用的，但是你可以使用 React 钩子来使它成为全局的！而且，它完全支持 typescript！

要了解更多信息，请看一下[文档](https://github.com/hitochan777/react-state-action-hooks)。
非常欢迎拉请求！