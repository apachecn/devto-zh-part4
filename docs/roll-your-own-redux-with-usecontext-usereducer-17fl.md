# 使用 useContext & useReducer 滚动您自己的 Redux

> 原文：<https://dev.to/gsto/roll-your-own-redux-with-usecontext-usereducer-17fl>

"大多数项目不够复杂，不需要 Redux . "

我经常从 React 社区听到这样的重复。我不是来争论它有多有效的。我所知道的是，在某些情况下，您确实希望在多个组件之间共享状态，并且您可能不希望将 Redux 引入您的架构。

在本教程中，您将学习如何创建自己的小型状态管理系统。我称他们为 reducklings。🦆

## 我们的使用案例:即时消息

默认情况下，Ruby on Rails 包含 flash 消息。在您的控制器中，您可以轻松地将消息显示在屏幕上。在我们的应用程序中，我们想要类似的东西:

*   在屏幕顶部显示一条或多条消息。
*   能够忽略一条消息。
*   有能力清除所有的信息。
*   任何组件都应该能够发送消息。

## 1:打造我们的减速器

因此，对于我们的消息队列，看起来我们有一个状态，我们希望对其执行几个操作。这是创建减速器的完美用例。看起来是这样的:

```
const messageReducer = (state, action) => {
  switch (action.type) {
    case 'ADD':
      return [
        ...state,
        action.payload,
      ]
    case 'CLEAR':
      return []
    case 'DISMISS':
      return state.filter((message, index) => index !== action.payload)
    default:
      return state
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 2:创建上下文

下一步，我们将使用 useReducer 创建一个状态数组和调度函数。但首先，我们需要一个存放它们的地方。这就是奇迹发生的地方。我们将在一个上下文中存储状态和调度，这样我们可以从任何地方访问它们。让我们通过创造我们的语境而存在:

```
const MessageContext = React.createContext({
  state: [],
  dispatch: null,
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 3:提供减速器

在我们的应用程序的顶层，或者您想要访问 duck 的最高层，您会想要将创建 reducer 的结果传递到上下文中。

```
import React, { useReducer } from 'react'
import { messageReducer, MessageContext } from './message_duckling
const App = () => {
  const [state, dispatch] = useReducer(messageReducer, [])
  return ( 
    <MessageContext.Provider value={{state, dispatch}}>
      {/* Your App Here */}
    </MessageContext>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

## 4:使用 UseContext 访问消息

让我们看看我们的第一个用例，读取组件中的消息。

```
import React, { useContext } from 'react'
import { MessageContext } from './message_context'
const MessageContainer = () => {
  const { state, dispatch } = useContext(MessageContext)
  return (
    <div className="messages-container">
      {state.map((message, index) => (
        <div className={`message ${message.type}`}>
          <span>{message.text}</span>
        </div>
      ))}
    </div>
  )
}
export default MessageContainer 
```

Enter fullscreen mode Exit fullscreen mode

## 5:调度动作

与 redux 类似，我们可以使用 dispatch 函数来更新消息。下面是一个创建消息的表单组件:

```
import React, { useState, useContext } from 'react'
import { MessageContext } from './message_context'
const MessageForm = () => {
  const [text, setText] = useState('')
  const { dispatch } = useContext(MessageContext)
  const createMessage = (e) => {
    e.preventDefault()
    const newMessage = { type: 'warning', text }
    dispatch({
      type: 'ADD',
      payload: newMessage
    })
  }
  return (
    <form onSubmit={createMessage}>
      <input type={text} onChange={e => setText(e.target.value)} />
      <input type="submit" value="post message" />
    </form>
  )
}
export default MessageForm 
```

Enter fullscreen mode Exit fullscreen mode

## 奖励积分:hoc 和定制挂钩

为了使您的代码更加清晰，您可以将 useReducer 包装成它自己的自定义钩子。为了使代码更具可扩展性，您还可以添加一个选项，允许用户定义自己的初始状态:

```
const useMessageReducer = (initial_state = []) {
  return useReducer(messageReducer, initialState)
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个有用的方法是创建一个高阶组件，将你的小鸭传递给任何函数。这样你就可以拥有类似 Redux 的 connect 函数的功能:

```
const withMessageStore = (WrappedComponent) => (props) => {
  const { state, dispatch } = useContext(MessageContext)
  return (<WrappedComponent
    {...props}
    messageStore={state}
    messageDispatch={dispatch}
  />) } 
```

Enter fullscreen mode Exit fullscreen mode

## 复习:一只小鸭子的特征

小鸭包括什么？

*   一个**上下文**，我们可以在应用程序的任何地方引用它。
*   该上下文带有一个全局**状态**和一个**分派**功能，让我们通过一个**缩减器编辑该状态。**可选地，它可以包括一个**定制钩子**和**高阶组件**以使其更容易实现。现在，让我们看看 Redux 中包含的一些我们这里没有的特性。

## 小鸭不是什么

*   一只小鸭子不提供**动作**、**类型**或**动作创建者**。
*   一只小鸭子不关心状态和调度映射。每个组件都获得整个状态和整个调度。这里没有 **mapStateToProps** 或**mapdispatctoprops**。
*   因此，我们也没有选择符。尽管你有可能建造它们。
*   它没有**中间件的概念。**

如果您认为您试图解决的问题需要更多这种功能，那么您知道您有更强的使用 Redux 的用例！不要接受这个建议，在不需要的时候重新发明轮子。相反，当你需要更小的轮子进行更短、更简单的旅行时，就使用它。

* * *

## 学分:

*   乔舒亚·富勒在 [Unsplash](https://unsplash.com/s/photos/duckling) 上拍摄的可爱的小鸭子照片。
*   [用使用上下文滚动自己的 Redux&useReducer](https://glennstovall.com/ducklings-roll-your-own-redux-with-usecontext-usereducer/)首次出现在[格伦·斯托瓦尔——工程师](https://glennstovall.com)。

* * *