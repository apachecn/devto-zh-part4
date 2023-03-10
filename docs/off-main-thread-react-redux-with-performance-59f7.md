# 脱离主线程反应降低性能

> 原文：<https://dev.to/dai_shi/off-main-thread-react-redux-with-performance-59f7>

##### 在 Web Workers 中运行 Redux

### 简介

据说 Redux 在一些用例中被过度使用，而 React context+hooks 在这样的用例中表现良好。虽然我同意它，Redux 应该在其他一些情况下工作良好。Redux 应该有助于许多开发者开发更大的应用。Redux 生态系统中的各种库应该加速发展。还有一种情况 Redux 可能会有所帮助，那就是 Web Workers。

不久前，苏尔马发表了一篇不错的博文:[React+Redux+Comlink = Off-main-thread](https://dassur.ma/things/react-redux-comlink/)

Redux 本身是独立于 UI 的东西，在一个 web worker 中运行 Redux 应该是简单合理的。博文展示了带有 [comlink](https://github.com/GoogleChromeLabs/comlink) 的概念验证代码。

但是，这个 PoC 代码在 React 上的表现并不好。让我引用博文中的一段话。

> 注意:[在 Twitter](https://twitter.com/nejcramovs/status/1156234576093687813) 上向我指出，通过将 Redux 移动到 worker，由于结构化克隆，每个状态更改都将导致新副本的创建。这可能是不好的，因为它将导致 React 重新呈现整个应用程序，而不仅仅是其状态属性已更改的元素。虽然我在*的这篇*博文中没有解决这个问题，但我在[的上一篇博文](https://dassur.ma/things/is-postmessage-slow/)的“补丁”部分谈到了一个解决方案。

我对解决这个问题如此感兴趣，以至于我情不自禁地停止开发新的库。

### 裁员

我的图书馆名为“redux-in-worker” [npm](https://www.npmjs.com) 里有那么多类似的库。希望这个库名有意义。

[https://github.com/dai-shi/redux-in-worker](https://github.com/dai-shi/redux-in-worker)

它是 Redux 存储的简单包装器。

在您的 worker 文件中，您像这样创建和公开一个存储:

```
// store.worker.js

import { createStore } from 'redux';
import { exposeStore } from 'redux-in-worker';

const reducer = ...;
const store = createStore(reducer);

exposeStore(store); 
```

Enter fullscreen mode Exit fullscreen mode

在您的应用程序文件中，您包装工人以获得正常的存储。

```
// app.js

import React from 'react';
import { Provider } from 'react-redux';
import { wrapStore } from 'redux-in-worker';

const initialState = ...;
const worker = new Worker('./store.worker', { type: 'module' });
const store = wrapStore(worker, initialState);

const App = () => (
  <Provider store={store}>
    ...
  </Provider>
); 
```

Enter fullscreen mode Exit fullscreen mode

有一点需要注意，您需要显式指定`initialState`。这是因为 worker 异步操作，在 worker 准备好并推送真正的 initialState 之前，我们需要 initialState。

对于那些对实现感兴趣的人，我鼓励他们阅读相当小的源代码。

有一些注释阐明了实现细节:

*   我没有使用 comlink，因为 Redux 不是 RPC，它本质上基本上是异步的。
*   我没有使用类似 immer 的补丁，因为它可能无法在 edge 情况下工作，并且我们不需要它，因为我们可以依赖 Redux 的不变性合同。
*   因此，不涉及代理，实现非常简单。

由于不变性，当前的实现应该具有足够的性能。基于代理的实现可能性能更好，也可能性能更差，但是如果有人感兴趣，我们将来会看到。

### 基准

现在，这就把我们带到了基准测试。

我们用 [js-framework-benchmark](https://github.com/krausest/js-framework-benchmark) 。已经有一个基准“react-redux-hooks”，我用 redux-in-worker 将它转换为“react-redux-hooks-worker”

这是结果。

[![screenshot1](img/e929870607115abed52766b7073e31b2.png "Benchmark result")](https://res.cloudinary.com/practicaldev/image/fetch/s--RotLSC1w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./screenshot1.png)

一眼看去，差别不大。这非常好，因为这意味着在这种情况下，区分、后期消息传递和打补丁的开销相对较低。

你可能会注意到“脚本启动时间”增加了一点。

有趣的是，redux-in-worker“创建 1000 行”更快，而“创建 10000 行”明显更慢。(我还有另一个实验，显示创建许多项目会变慢。)

### 期末备注

脱离主线程的 Redux 相对容易，但是在实践中，当涉及到 React Redux 时，会遇到一些障碍。最值得注意的一个应该是`redux-thunk`，它经常在许多 React Redux 应用程序和 Redux Starter Kit 中使用。因为我们不能将功能分派给一个工人，所以 thunks 根本不起作用。另一个困难是基于 DOM 的中间件，比如 connected-react-router。我个人建议将与 DOM 相关的状态从 Redux 中转移到 React 本地状态或上下文中。但这不会在一夜之间发生，而且有如此多的现有应用和最佳实践。

最后，就偏离主方向的 React Redux 而言，Redux 的部分通常小于 React。已经有几个非主线程 React 渲染的实验，我认为这是一个非常有趣的想法。但这不是小事。我不知道他们中是否有人达到了实际工作的程度。最后一点，React 团队似乎在推动并发模式，而不是 web workers。

* * *

最初发表于 2019 年 9 月 16 日[https://blog.axlight.com](https://blog.axlight.com/posts/off-main-thread-react-redux-with-performance/)。