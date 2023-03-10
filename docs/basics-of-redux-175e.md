# Redux 的基础知识

> 原文：<https://dev.to/dastnbek/basics-of-redux-175e>

## **目录**

*   [Redux 是什么？](#redux)
*   [redux 的生命周期方法](#lifecycle)
*   [生命周期方法的定义](#def)

### **什么是 Redux**

Redux 是国家管理图书馆。Redux 与 React、Vue 或 Angular 等用户界面库的常见用例。然而，您可以将这个库添加到您的任何 web 项目中，以控制您的全局状态。[更](https://redux.js.org/)

### **生命周期方法**

[![](img/a31e43509d935b60bce2ee8b7afd89bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LveMVqIe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rmtgljl6f3lkwfqqcsua.png)

### **定义**

*动作创建器*是创建或返回普通 javascript 对象的函数。
*动作*是普通的 javascript 对象。所以，动作创建者返回动作。动作必须有定义动作目的的*类型*和可选的包含数据的*有效载荷*。

```
// Action creator                 // Action
const myActionCreator = value => {type : "INCREASE_TO_ONE", paylaod : value } 
```

*分派*是采取行动并复制该对象的函数。它是内置的功能。

*减速器*是根据被调度的动作类型采取适当动作并更新状态的功能。

```
// Reducer
const myReducer = (prevState = null, action) => {
 if(action.type === "INCREASE_TO_ONE"){
    prevState = action.payload + 1;
    return prevState;
 }
 return prevState;
} 
```

*State* 是您的应用程序的实际状态值；
下面给出了使用 Redux
的例子

```
// import built in libraries from redux
import {createStore, combineReducers} from 'redux'

// Action creator                 // Action
const myActionCreator = value => {return {type : "INCREASE_TO_ONE", paylaod : value }} ;

// Reducer
const myReducer = (prevState = 0, action) => {
 if(action.type === "INCREASE_TO_ONE"){
    prevState = action.paylaod + 1;
    return prevState;
 }
 return prevState;
}

const combinedReducers = combineReducers({value : myReducer})
const store = createStore(combinedReducers);
const action = myActionCreator(6);
// use case of dispatch function
store.dispatch(action);

console.log(store.getState()); 
```

测试 [codesandbox.io](https://codesandbox.io/s/trusting-visvesvaraya-e0ekb) 中的代码

### **就是它！！！**