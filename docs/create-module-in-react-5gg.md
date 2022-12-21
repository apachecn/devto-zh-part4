# 在 React 中创建模块

> 原文：<https://dev.to/aziziyazit/create-module-in-react-5gg>

React 中没有定义任何模块，众所周知，React 将代码结构的完全控制权交给了开发人员。

## 模块

模块可以用来优化代码结构。在 React 中，我喜欢作为提供者创建一个模块。这是因为提供者是向其消费者(孩子)提供道具、回调和数据的人。

### 模块作为提供者

```
const ModuleAContext = createContext({})
const initialState = { stateA: '', stateB: false, stateC: {}}

function ModuleA (props) {
 const [providerState, dispatch] = useReducer(reducer, initialState)
 const moduleAContextValue = {...props, providerState, dispatch}

 function reducer (state, action) {
  switch (action.type) {
   case STATE_A_CHANGED: 
    return { ...state, stateA: action.stateA }
   case STATE_B_CHANGED: 
    return { ...state, stateB: action.stateB }
   case STATE_C_CHANGED: 
    return { ...state, stateB: action.stateC }
   default: 
    return initialState
  }
 }

 return (
  <ModuleAContext.Provider value={moduleAContextValue}>
   <ChildA />
   <ChildB />
   <ChildC />
  </ModuleAContext.Provider>
 )

} 
```

作为提供者，moduleA 不仅负责提供数据和回调，还负责管理其子节点中发生的事件(调度)。

### 创建模块子代作为消费者

```
function ChildA () {
 const moduleAContext = useContext(ModuleAContext)

 function handleClick (a) {
  dispatch({type: STATE_A_CHANGED, stateA: a })
 }

 return (
  <div onClick={handleClick}>{moduleAContext.stateA}</div>
 )
}

function ChildB () {
 const moduleAContext = useContext(ModuleAContext)

 function handleClick (b) {
  dispatch({type: STATE_B_CHANGED, stateB: b })
 }

 return (
  <div onClick={handleClick}>{moduleAContext.stateB}</div>
 )
}

function ChildC () {
 const moduleAContext = useContext(ModuleAContext)

 function handleClick (c) {
  dispatch({type: STATE_C_CHANGED, stateC: c })
 }

 return (
  <div onClick={handleClick}>{moduleAContext.stateC}</div>
 )
} 
```

消费者是模块使用的组件。它们通过模块的上下文(ModuleAContext)进行通信。

### 总结

我们使用上下文 API 和提供者-消费者模式来暴露模块属性、模块状态和对其子模块(消费者)的回调。在代码系统中添加模块有助于开发人员将复杂的组件设计成生活在一个模块下的组件的混合物。

### 下一系列

我们将讨论“在 React 中创建页面”,其中页面是包含许多模块的容器。我们将为模块通信使用定制的钩子。