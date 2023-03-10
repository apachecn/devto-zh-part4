# useReducer vs useState 使用 useReducer()而不是 useState()的三个原因

> 原文：<https://dev.to/spukas/3-reasons-to-usereducer-over-usestate-43ad>

## 这是什么

`useReducer()`是 React Hooks API 的一个方法，类似于`useState`,但是给了你更多的控制来管理状态。它以一个 reducer 函数和初始状态作为参数，返回状态和调度方法:

```
const [state, dispatch] = React.useReducer(reducerFn, initialState, initFn); 
```

Enter fullscreen mode Exit fullscreen mode

reducer(之所以这么叫，是因为您将传递给数组方法`Array.prototype.reduce(reducer, initialValue)`的函数类型)是从 Redux 中提取的模式。如果你不熟悉 Redux，简而言之，reducer 就是一个纯函数，把前一个状态和动作作为自变量，返回下一个状态。

```
(prevState, action) => newState 
```

Enter fullscreen mode Exit fullscreen mode

动作是描述发生了什么的一条信息，基于这条信息，reducer 指定状态应该如何改变。动作通过`dispatch(action)`方法传递。

## 使用它的 3 个理由

大多数时候，你只需要使用建立在`useReducer()`之上的`useState()`方法就可以了。但是有些情况下`useReducer()`更好。

### 下一个状态取决于上一个

当状态依赖于前一种方法时，使用这种方法总是更好。它会给你一个更可预测的状态转换。简单的例子是:

```
function reducer(state, action) {
  switch (action.type) {
    case 'ADD': return { count: state.count + 1 };
    case 'SUB': return { count: state.count - 1 };
    default: return state;
  }
}

function Counter() {
  const [state, dispatch] = React.useReducer(reducer, { count: 0 });
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'ADD'})}>Add</button>
      <button onClick={() => dispatch({type: 'SUB'})}>Substract</button>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

### 复杂的状态形状

当状态由不止一个原始值组成时，如嵌套对象或数组。例如:

```
const [state, dispatch] = React.useReducer(
  fetchUsersReducer,
  {
    users: [
      { name: 'John', subscribred: false },
      { name: 'Jane', subscribred: true },
    ],
    loading: false,
    error: false,
  },
); 
```

Enter fullscreen mode Exit fullscreen mode

管理这种本地状态更容易，因为参数相互依赖，所有的逻辑都可以封装在一个缩减器中。

### 易于测试

Reducers 是纯函数，这意味着它们没有副作用，并且在给定相同参数的情况下必须返回相同的结果。测试它们更容易，因为它们不依赖于 React。让我们从反例中取出一个缩减器，用模拟状态测试它:

```
test("increments the count by one", () => {
  const newState = reducer({ count: 0 }, { type: "ADD" });
  expect(newState.count).toBe(1)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

`useReducer()`是`useState()`的替代，它给你更多的状态管理控制，并且可以使测试更容易。所有的案例都可以用`useState()`方法来完成，所以总而言之，用你觉得舒服的方法，你和同事都更容易理解。