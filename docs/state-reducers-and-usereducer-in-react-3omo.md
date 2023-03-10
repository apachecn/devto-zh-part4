# React 中的状态、还原器和已用还原器

> 原文：<https://dev.to/stephencweiss/state-reducers-and-usereducer-in-react-3omo>

迄今为止，我一直在努力理解减速器。像 reducers、actions、dispatch 这样的术语，都模糊在一起，即使我可以在一些项目中使用 redux store，在某些情况下，我也无法理解所有的部分。

尽管阅读了关于`useReducer`的 hooks 文档，但直到我阅读了 Robin Wieruch 的关于减速器的两部分教程，这些部分才合二为一。

那么，到底是怎么回事？

## useReducer

`useReducer`返回一个元组`[state, dispatch]`，并接受三个参数:`reducer`、`initialArg`和`init`。注意:`init`是可选的，用于惰性初始化——稍后会详细介绍。

在一个 Javascript 文件中，您会看到类似于:

```
import React, { useReducer } from 'react';
...

function FunctionalComponent() => {
  const [state, dispatch] = useReducer(reducer, initialArg, init);
  return (
    <>
        {/* ... */}
    </>
  )
}; 
```

请注意，在这一点上，这看起来非常类似于`useState` :

```
import React, { useState } from 'react';
...

function FunctionalComponent() => {
  const [value, setValue] = useState(initialValue);
  return (
    <>
        {/* ... */}
    </>
  )
}; 
```

事实上，即使`initialValue`比`boolean`或`string`更奇特，我们仍然可以使用`useState`。我们只需要使用函数更新语法。

例如，改编 React 团队的例子:

```
const initialValues = {
  buttonOne: 0,
  buttonTwo: 0,
}

function Counter() {
  const [count, setCount] = useState(initialValues);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialValues)}>Reset</button>
      <button onClick={() => setCount(prevCount => {...prevCount, prevCount.buttonOne + 1)}>+</button>
      <button onClick={() => setCount(prevCount => {...prevCount, prevCount.buttonTwo - 1)}>-</button>
    </>
  );
} 
```

这个例子不是很有用，因为两个值只能反向，但是它说明了我们如何使用`useState`来管理更复杂的状态对象。

## 为什么 useReducer？

既然我们可以用`useState`管理状态，为什么我们还需要`useReducer`？React 团队表示:

> 当您的复杂状态逻辑涉及多个子值或者下一个状态依赖于前一个状态时，`useReducer`通常比`useState`更好。useReducer 还允许您优化触发深度更新的组件的性能，因为[您可以向下传递分派，而不是回调](https://reactjs.org/docs/hooks-faq.html#how-to-avoid-passing-callbacks-down)。—反应团队

## 使用减速器

`useReducer`接受一个`reducer`，一个`(state, action) => newState`形式的函数。

让我们暂时简化一下我们的例子，*只是*添加数字，但是使用`useReducer` :

```
const initialValues = 0;

function reducer = (state, action) => {
return state + 1
}

function Counter() {
  const [state, dispatch] = useState(reducer, initialValues);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch()}>+</button>
    </>
  );
} 
```

我们*之所以只在这里*加数字，是因为我们的`reducer`没有*使用*第二个自变量`action`。已经修好了。

我们如何改变这种情况？

## 动作

行动是我们改变现状的方式。

来自 Redux 文档:

> 动作是将数据从应用程序发送到商店的信息负载。他们是商店唯一的信息来源。⁴

这里有一个使用最简单动作的例子——再次引入我们的第二个按钮:

```
const initialValues = 0;

function reducer = (state, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
}

function Counter() {
  const [state, dispatch] = useState(reducer, initialValues);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'INCREMENT'})}>+</button>
      <button onClick={() => dispatch({type: 'DECREMENT'})}>-</button>
    </>
  );
} 
```

当我们点击`+`时，我们分派动作来增加，而`-`分派动作。那些动作被我们的 reducer 评估并返回一个新的状态。

### 有效载荷

编写动作的惯例是同时拥有一个`type`和一个`payload`键。而`type`就是*什么*，而`payload`就是*如何*。在这种情况下，它没有多大意义，因为我们一直使用的状态只是一个整数，但如果它是更复杂的东西，会发生什么呢？我们该如何改变它呢？

让我们想象一个状态对象，它有 count *和 person 属性*。

```
const initialValues = {
  count: 0,
  person: {
    firstName: 'John',
    lasttName: 'Doe',
    age: '30',
  },
};

function reducer = (state, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return {...state, count: state.count + action.payload};
    case 'DECREMENT':
      return {...state, count: state.count - action.payload}
    default:
      throw new Error(`Unknown action type, ${action.type}`);
}

function Counter() {
  const [state, dispatch] = useState(reducer, initialValues);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'INCREASE', payload: 1})}>+</button>
      <button onClick={() => dispatch({type: 'DECREASE', payload: 1})}>-</button>
    </>
  );
} 
```

注意:在这种情况下，我们在修改`count`属性之前*扩展*状态对象，这样我们就不会覆盖整个对象*并且*避免计数的新值被覆盖(顺序问题)。

### 懒惰初始化

现在我们知道了如何使用动作，我们可以把它们放在一起，看看如何使用惰性初始化。

例如:

```
function init(initialValues){
return (
  { count: 0,
    person: {
      firstName: 'John',
      lasttName: 'Doe',
      age: '30'
    },
  }
)};

function reducer = (state, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return {...state, count: state.count + action.payload};
    case 'DECREMENT':
      return {...state, count: state.count - action.payload}
    case 'RESET':
      return init(action.payload);
    default:
      throw new Error(`Unknown action type, ${action.type}`);
}

function Counter() {
  const [state, dispatch] = useState(reducer, initialValues, init);
  return (
    <>
      <button onClick={() => dispatch({type: 'RESET', payload: initialValues})>Reset</button>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'INCREASE', payload: 1})}>+</button>
      <button onClick={() => dispatch({type: 'DECREASE', payload: 1})}>-</button>
    </>
  );
} 
```

这经常用在类似上面的例子中，我们想要提取在初始设置的之外重置值*的能力。我们在上面用`Reset`按钮元素来做这件事。*

## 结论

当我遇到一个使用 Redux 或其他状态管理工具的项目时，我从未真正理解它是如何工作的。我可以用它，但是我从来不觉得舒服。

通读完 Robin 的教程后，我能够带着全新的眼光回来，并在我自己的项目中实现它。事情一拍即合的感觉太棒了！希望这篇文章能帮助其他人体验同样的感觉。

我错过了什么吗？如果有，请告诉我！

## 脚注

*   [挂钩 API 参考–反应](https://reactjs.org/docs/hooks-reference.html#usereducer)
*   [JavaScript 中的 reducer (React/Redux)是什么？| RWieruch](https://www.robinwieruch.de/javascript-reducer/)
*   如何在 React 中使用 Reducer？| RWieruch
*   ⁴ [动作|还原](https://redux.js.org/basics/actions)