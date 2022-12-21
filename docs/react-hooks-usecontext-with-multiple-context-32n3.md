# React 挂钩——在多个上下文中使用一个上下文

> 原文：<https://dev.to/rjain15/react-hooks-usecontext-with-multiple-context-32n3>

这是我的问题/困惑。

我按照一些代码示例来创建 GlobalState，然后使用 react 钩子 useContext 来访问状态。

下面是示例代码
[https://github . com/academind/react-redux-vs-context/blob/context-hooks/src/context/global state . js](https://github.com/academind/react-redux-vs-context/blob/context-hooks/src/context/GlobalState.js)

还有例子视频
[https://www.youtube.com/watch?v=R_7XRX7nLsw](https://www.youtube.com/watch?v=R_7XRX7nLsw)

我的问题是，如果我有多个组件，每个组件都试图访问全局状态，我是创建一个全局上下文还是创建多个上下文。然后，如果我有多个上下文，我如何使用提供者和消费者。