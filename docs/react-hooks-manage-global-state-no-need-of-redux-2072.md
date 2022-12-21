# react-hooks:管理全局状态-不需要 redux

> 原文：<https://dev.to/krishnaprasad2141/react-hooks-manage-global-state-no-need-of-redux-2072>

我知道你想管理全局状态，使用 redux 是 2018 年的事情。记住 redux 很棒，但是如果你没有要求，就不要使用它。
那么，有什么方法可以不使用 redux 来管理全局状态呢？

是的，答案是“上下文 api”和“useReducer”。

在本教程中，您将通过一系列步骤了解全局管理状态所需的每一件事情。

让我们开始吧。

在 App.tsx 中

第一步。
导入 React，{useReducer}来自“React”；
干得好！

第二步。
在你的 App.tsx 中用 Reducer 和初始计数调用 useReducer(countReducer，initialCount)。

哦！不要！减速器是什么鬼东西，你从哪里弄来的。

别担心 reducer 只是一个接受两个参数(状态，动作)的函数

第 1.5 步。

让我们先创建一个缩减器

const countReducer = (state，action)= > {
switch(action . type){
case " increment ":
返回状态+1；
破；
案例“减量”:
返回状态-1；
破；
情况“复位”:
返回 0；
破；
默认:
返回 0；
破；
}
}；
const initial count = 0；

omg 你刚刚创建了 reducer 和初始状态
是不是很有趣

再次第二步。

用 countReducer 和 initialCount 调用 useReducer。你将得到当前状态和一个调用我们上面创建的 reducer 的调度方法。

const [countState，count dispatch]= useReducer(count reducer，initial count)；
useReducer 有两个参数

你可能会问为什么要用这么难看的数组。相信我，这只是合成糖，当我看到这个的时候，我也有同样的感觉。

所以基本上它是数组析构。它不是数组，只是两个正常变量

代替写
const countState = blablabla 和
const count dispatch = blablabla

我写了 const [countState，countDispatch] = blablabla

它只是整洁和干净

useReducer 返回一个由两个元素组成的数组
state——表示当前状态
dispatch 方法——来调度 Reducer 中的动作

休息一下。

创造环境。对于第一次导入，从 react 创建一个上下文

从“react”导入 React，{ useReducer，create context }；
创造语境。

export const counter context = create context(null)；

我们可以通过上下文将分派方法从一个组件传递到另一个组件。提供程序并更新全局状态。

在第一个文件

中导入 react，从“React”中{ useReducer，create context }；

const initial state = 0；

export const counter context = create context(null)；
T7】const App:React。FC =()=>{

const[count，dispatch]= useReducer(count reducer，initial state)；

回车(

Count: {count}

);

};

export default App;

In second file

import React, { useContext } from "react";

import { CounterContext } from "./App";

export const Counter = () => {

const context = useContext(CounterContext);

return (

# {上下文.计数}

<button>context.dispatch({ type: "increment" })}>+</button>

<button>context.dispatch({ type: "decrement" })}>-</button>

<button>context.dispatch({ type: "reset" })}>0</button>

## -

);

};