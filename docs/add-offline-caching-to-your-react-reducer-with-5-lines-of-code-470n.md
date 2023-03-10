# 用 5 行代码向 React Reducer 添加离线缓存

> 原文：<https://dev.to/rakannimer/add-offline-caching-to-your-react-reducer-with-5-lines-of-code-470n>

在构建 web 应用程序时，我经常使用这种方法来添加我的状态的本地存储缓存。

假设我们从:
开始

```
 const [state, dispatch] = React.useReducer(
  reducer,
  initialState
) 
```

作为第一步，我们创建一个方法，接受一个缩减器并输出另一个，一个高阶函数:

```
const withLocalStorageCache = reducer => { return (state, action) => {
  const newState = reducer(state, action);
  localStorage.setItem("my-state", JSON.stringify(newState));
  return newState;
}}; 
```

在将结果传递给`useReducer`钩子之前，我们用它包装我们的减速器。

```
 const [state, dispatch] = React.useReducer(
  withCache(reducer),
  initialState
) 
```

最后，我们用它的最新缓存版本替换 initial state:

```
const [state, dispatch] = React.useReducer(
  withCache(reducer),
  localStorage.getItem("my-state")
) 
```

就这样。

修改了一行代码和 5 行新代码，我们的整个状态与 localStorage 同步。

当然，我们可以将缓存限制在状态的特定部分，而不是整个状态对象。并通过动作或状态限制我们对缓存的更新。但是我们将把它作为一个练习留给用户。

> 97.62%的浏览器支持本地存储。

完整演示

[https://codesandbox.io/embed/add-offline-caching-to-your-react-reducer-t6zds](https://codesandbox.io/embed/add-offline-caching-to-your-react-reducer-t6zds)