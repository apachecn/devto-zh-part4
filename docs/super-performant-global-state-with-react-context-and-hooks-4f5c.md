# 具有 React 上下文和钩子的高性能全局状态

> 原文：<https://dev.to/dai_shi/super-performant-global-state-with-react-context-and-hooks-4f5c>

##### 又一个类似 Redux 的库

### 简介

React 中有许多库提供全局状态。React 本身没有提供这样的特性，可能是因为关注点的分离很重要，天真地拥有全局状态并不习惯。然而，在某些情况下，只要实现得当，拥有全局状态是好的。与使用非全局状态(包括多个上下文)相比，性能可能会下降。

这篇文章介绍了一个具有性能的全局状态库。

### 问题

将 context 和 useReducer 结合起来，开发一个类似 Redux 的特性是很容易的。有人会说，如果他们不需要 Redux DevTools 和 Redux 中间件，这就足够了。

但是，如果一个应用程序变得更大，就会有一个问题。从技术上讲，useContext 没有脱离机制，所有使用 Context 的组件在每次 Context 值改变时都要重新呈现。这就是 react-redux 放弃直接使用上下文，转而使用订阅的原因。

无论如何，如果你为一个大的状态对象使用上下文值，这个问题就会发生。除非你的 app 非常小，否则这个限制是不能忽略的。

另一个问题是如何指定组件需要呈现状态的哪一部分。选择器经常在这样的场景中使用，但是编写合适的选择器并不容易，除非您对引用相等和记忆化有很好的了解。

### 解

第一个问题通过在上下文值改变时停止上下文传播来解决。这是由名为“calculateChangedBits”的未记录功能完成的。因为传播停止了，所以没有更新被推送到组件，现在组件需要拉取更改。我们使用订阅。一些有经验的开发人员可能会想，如果我们使用订阅，为什么我们仍然需要使用上下文。这是一个假设，但是使用上下文对于并发模式更安全，并且可能更适合 React 开发人员工具。

第二个问题通过跟踪组件呈现中的状态使用来解决。这是通过代理完成的。有点神奇，但基本上只是为了性能优化。它根本没有改变语义。

### 库

我将这些特性实现为一个库。

[https://github.com/dai-shi/react-tracked](https://github.com/dai-shi/react-tracked)

在写作时它还是新的，但是已经准备好接受审查了。

### 举例

```
import React, { useReducer } from 'react';
import ReactDOM from 'react-dom';

import { Provider, useTracked } from 'react-tracked';

const initialState = {
  counter: 0,
  text: 'hello',
};

const reducer = (state, action) => {
  switch (action.type) {
    case 'increment': return { ...state, counter: state.counter + 1 };
    case 'decrement': return { ...state, counter: state.counter - 1 };
    case 'setText': return { ...state, text: action.text };
    default: throw new Error(`unknown action type: ${action.type}`);
  }
};

const useValue = () => useReducer(reducer, initialState);

const Counter = () => {
  const [state, dispatch] = useTracked();
  return (
    <div>
      {Math.random()}
      <div>
        <span>Count:{state.counter}</span>
        <button type="button" onClick={() => dispatch({ type: 'increment' })}>+1</button>
        <button type="button" onClick={() => dispatch({ type: 'decrement' })}>-1</button>
      </div>
    </div>
  );
};

const TextBox = () => {
  const [state, dispatch] = useTracked();
  return (
    <div>
      {Math.random()}
      <div>
        <span>Text:{state.text}</span>
        <input value={state.text} onChange={event => dispatch({ type: 'setText', text: event.target.value })} />
      </div>
    </div>
  );
};

const App = () => (
  <Provider useValue={useValue}>
    <h1>Counter</h1>
    <Counter />
    <Counter />
    <h1>TextBox</h1>
    <TextBox />
    <TextBox />
  </Provider>
);

ReactDOM.render(<App />, document.getElementById('app')); 
```

Enter fullscreen mode Exit fullscreen mode

### 演示

[codesandbox](https://codesandbox.io/s/github/dai-shi/react-tracked/tree/master/examples/01_minimal)

### 期末备注

我没有解释图书馆的一切。最值得注意的是，这个库是 [reactive-react-redux](https://github.com/dai-shi/reactive-react-redux) 的一个分支，实际上钩子 API 是相同的，这也类似于 [react-redux 钩子](https://react-redux.js.org/api/hooks)。如果你是一个 redux 用户，并且已经确信 DevTools 和中间件，就使用那些库。

* * *

最初发表于 2019 年 6 月 15 日[https://blog.axlight.com](https://blog.axlight.com/posts/super-performant-global-state-with-react-context-and-hooks/)。