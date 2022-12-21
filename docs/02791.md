# 我是如何为网络工作者开发 React 钩子的

> 原文：<https://dev.to/dai_shi/how-i-developed-react-hooks-for-web-workers-4cci>

##### 利用异步发电机

### 简介

我一直在开发几个 react hooks 库。他们为特定的目的提供定制的钩子。其中一个是给网络工作者的。我开始是为了好玩。得到了一些反馈，改进了。这篇文章展示了当前的实现，目标是在生产中使用。

在这个领域， [Comlink](https://github.com/GoogleChromeLabs/comlink) 提供了一个很好的带有代理的透明 API。有些人可能已经用 React 试过了。我有两个理由不把它用于我的图书馆。

1.  React 钩子本质上是反应性的，所以不需要异步接口。对于 Comlink，主线程中的 API 是一个异步函数。你需要把`await`放在`Comlink.wrap`前面。有了 React，我们可以在钩子中隐藏异步行为。

2.  RPC 样式是有限的。网络工作者通常用于耗时的任务。为了更好的 UX，我们可能需要显示任务的进展或中间结果。

### 库

我开发了一个库来提供一个定制的钩子，方便工人使用。它没有依赖性，代码也很小。

[https://github.com/dai-shi/react-hooks-worker](https://github.com/dai-shi/react-hooks-worker)

### 基本用法

这里有一个计算斐波那契数列的基本例子。你需要两个文件用于工作线程和主线程。该库为每个文件导出两个函数。

工人文件如下所示。

```
// fib.worker.js

import { exposeWorker } from 'react-hooks-worker';

const fib = i => (i <= 1 ? i : fib(i - 1) + fib(i - 2));

exposeWorker(fib); 
```

Enter fullscreen mode Exit fullscreen mode

react 文件如下所示。

```
// App.jsx

import React from 'react';
import { useWorker } from 'react-hooks-worker';

const createWorker = () => new Worker('./fib.worker', { type: 'module' });

const CalcFib = ({ count }) => {
  const { result, error } = useWorker(createWorker, count);
  if (error) return <div>Error: {error}</div>;
  return <div>Result: {result}</div>;
};

export const App = () => (
  <div>
    <CalcFib count={5} />
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

### 异步发电机

正如我暗示的，这个库提供了非 RPC 接口。我们使用(异步)生成器来返回中间状态。

这里有一个例子来展示斐波那契数列的计算步骤。

```
// fib-steps.worker.js

import { exposeWorker } from 'react-hooks-worker';

async function* fib(x) {
  let x1 = 0;
  let x2 = 1;
  let i = 0;
  while (i < x) {
    yield `(calculating...) ${x1}`;
    await new Promise(r => setTimeout(r, 100));
    [x1, x2] = [x2, x1 + x2];
    i += 1;
  }
  yield x1;
}

exposeWorker(fib); 
```

Enter fullscreen mode Exit fullscreen mode

### 实现

`exposeWorker`的实现出奇的简单。

```
export const exposeWorker = (func) => {
  self.onmessage = async (e) => {
    const r = func(e.data);
    if (r[Symbol.asyncIterator]) {
      for await (const i of r) self.postMessage(i);
    } else if (r[Symbol.iterator]) {
      for (const i of r) self.postMessage(i);
    } else {
      self.postMessage(await r);
    }
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

`useWorker`的实现可以是各种风格。目前，它是用 useReducer 实现的。

```
import {
  useEffect,
  useMemo,
  useRef,
  useReducer,
} from 'react';

const initialState = { result: null, error: null };
const reducer = (state, action) => {
  switch (action.type) {
    case 'init':
      return initialState;
    case 'result':
      return { result: action.result, error: null };
    case 'error':
      return { result: null, error: 'error' };
    case 'messageerror':
      return { result: null, error: 'messageerror' };
    default:
      throw new Error('no such action type');
  }
};

export const useWorker = (createWorker, input) => {
  const [state, dispatch] = useReducer(reducer, initialState);
  const worker = useMemo(createWorker, [createWorker]);
  const lastWorker = useRef(null);
  useEffect(() => {
    lastWorker.current = worker;
    let dispatchSafe = action => dispatch(action);
    worker.onmessage = e => dispatchSafe({ type: 'result', result: e.data });
    worker.onerror = () => dispatchSafe({ type: 'error' });
    worker.onmessageerror = () => dispatchSafe({ type: 'messageerror' });
    const cleanup = () => {
      dispatchSafe = () => null; // we should not dispatch after cleanup.
      worker.terminate();
      dispatch({ type: 'init' });
    };
    return cleanup;
  }, [worker]);
  useEffect(() => {
    lastWorker.current.postMessage(input);
  }, [input]);
  return state;
}; 
```

Enter fullscreen mode Exit fullscreen mode

重要提示:如果`createWorker`与前一个引用不同，它会停止前一个工人并启动一个新工人。否则，它将重用该工作实例。目前没有办法通过对单个 worker 实例的多次调用来区分结果。

### 期末备注

如果我们使用 worker 来处理非平凡的用例，我们可能会在 worker 中使用一些库。这需要更多的支持。到目前为止，我只在 webpack 中用 [worker-plugin](https://github.com/GoogleChromeLabs/worker-plugin) 试过。webpack 中还有其他插件。其他捆绑器支持类似的功能。欢迎您试用它们，并向项目报告结果。

* * *

最初发表于 2019 年 9 月 8 日 https://blog.axlight.com。