# 带有 React 挂钩的全局状态

> 原文：<https://dev.to/rahmanfadhil/global-state-with-react-hooks-15fm>

*阅读原文[此处](https://rahmanfadhil.com/global-state-hooks)T3】*

[React 钩子](https://reactjs.org/docs/hooks-intro.html)允许你在功能组件中使用状态和其他 React 特性。另一方面， [React Context](https://reactjs.org/docs/context.html) 提供了一种在组件树中传递数据的方法，无需手动传递。

将这两个特性结合起来，我们现在可以构建自己的状态管理，而无需使用第三方库。除了使我们的 web 应用程序更轻便之外，我们的代码也更加高效和可读。

## 免责声明

首先声明，这些方法不是管理全局状态的最佳实践(使用 [Redux](https://redux.js.org/) 代替)。第二，有一些第三方库与我们在本教程中所做的相似。如果这些是你正在寻找的，一定要看看像[下一个](https://github.com/jamiebuilds/unstated-next)或[简单易行](https://github.com/jamiebuilds/unstated-next)这样的东西。

## 我们要建造什么？

我们将建立一个简单的计数器应用程序，只需点击一个按钮增加一个数字。我们的状态管理系统将看起来像 Redux(使用 reducer 函数和 dispatcher)。最后，我们将能够通过合并两个(或更多)reducers 函数来扩展我们的应用程序(第 2 部分)。

我已经在这里发布了这个项目的完整版本。

## 全局状态挂钩

```
// ./useGlobalState.js

import React, { createContext, useReducer, useContext } from "react"

const StateContext = createContext(null)

export function Provider({ children, reducer, initialState }) {
  return (
    <StateContext.Provider value={useReducer(reducer, initialState)}>
      {children}
    </StateContext.Provider>
  )
}

export function useGlobalState() {
  return useContext(StateContext)
} 
```

我们的国家管理非常直接。我们将我们的**整个**状态存储在我们的`StateContext`上下文中，然后我们有一个`Provider`组件，稍后我们将使用它来提供我们的全局状态，使我们的组件树中的所有组件都可以访问它。

最后，我们定义了我们的`useGlobalState`定制钩子，我们将在我们想要访问全局状态的组件内部使用它。

## 提供状态

我们已经准备好了我们的状态管理，现在我们需要创建我们的状态并在`App`组件中提供它。

首先，我们声明我们的 reducer 函数来改变我们的状态。目前，我们只能处理`INCREMENT`动作来增加我们的计数器状态。

```
function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return state + 1
    default:
      return state
  }
} 
```

你注意到我们通过返回一个新值来改变我们的状态。那是因为我们的状态是不可变的，我们不能通过重新赋值来改变我们的状态。这是使用`useReducer`时要记住的一件重要事情。

然后，我们需要定义我们的初始状态。我们的计数器应用程序应该从 0 开始。

```
const initialState = 0 
```

最后，我们的`App`必须为我们的状态提供`Provider`组件，以便我们的组件树中的其他组件可以访问它。`Provider`组件需要减速器功能和一个初始状态作为道具。让我们按照前面的定义来定义它们。

```
export default function App() {
  return (
    <Provider reducer={reducer} initialState={initialState}>
      <Counter />
    </Provider>
  )
} 
```

如果你做得对，你的代码应该是这样的。

```
// ./App.js

import React from "react"

import { Provider } from "./useGlobalState"
import Counter from "./Counter"

function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return state + 1
    default:
      return state
  }
}

const initialState = 0

export default function App() {
  return (
    <Provider reducer={reducer} initialState={initialState}>
      {/* Place your components here */}
    </Provider>
  )
} 
```

## 访问全局状态

为了测试我们的状态工作，让我们尝试从`Counter`组件访问我们的全局状态。

```
// ./Counter.js

import React from "react"
import { useGlobalState } from "./useGlobalState"

export default function Counter() {
  const [state, dispatch] = useGlobalState()

  return (
    <div>
      <h1>Counter: {state}</h1>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>Increase</button>
    </div>
  )
} 
```

在这个组件中，我们在一个`h1`中显示我们的状态。每当我们的“增加”按钮被点击时，我们试图调用`INCREMENT`动作。

尝试运行应用程序。如果您没有看到任何错误，那么您就正确地遵循了本教程。

以下是该项目的代码:

[https://codesandbox.io/embed/globalstatehooks-g8gru](https://codesandbox.io/embed/globalstatehooks-g8gru)