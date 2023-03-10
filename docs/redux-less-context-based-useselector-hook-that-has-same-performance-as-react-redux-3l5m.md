# Redux-less 基于上下文的 useSelector 挂钩具有与 React-Redux 相同的性能

> 原文：<https://dev.to/dai_shi/redux-less-context-based-useselector-hook-that-has-same-performance-as-react-redux-3l5m>

##### 超级好用的 TrackedState 钩子也推荐

### 简介

React-Redux 为[钩子 API](https://react-redux.js.org/api/hooks)
提供了很好的抽象。尤其是，`useSelector`可能比`mapStateToProps`更少被误用。

[react-tracked](https://github.com/dai-shi/react-tracked) 是一个没有 Redux 的全局状态库。这个库提供了几乎兼容的 hooks API 来 React-Redux。它的开发考虑到了性能，应该和 React-Redux 一样高性能，即使它只利用 React 上下文。有关更多信息，请参见 GitHub repo。

[https://github.com/dai-shi/react-tracked](https://github.com/dai-shi/react-tracked)

这篇文章展示了基准测试结果，以证明它在一个场景中确实是高性能的。

值得一提的是，react-tracked，顾名思义，有另一个`useTrackedState`钩子，能够跟踪状态使用情况。与`useSelector`不同，开发者不需要为这个钩子定义选择器。

另外值得一提的是，另一个库 [reactive-react-redux](https://github.com/dai-shi/reactive-react-redux) 为 redux 提供了`useTrackedState`钩子。有关更多信息，请参见 GitHub repo。

[https://github.com/dai-shi/reactive-react-redux](https://github.com/dai-shi/reactive-react-redux)

我们比较上面描述的所有钩子。

### 基准工具

为了对不同的框架进行基准测试，使用了 [js-framework-benchmark](https://github.com/krausest/js-framework-benchmark) 。

如果你想在你那端重现基准结果，请查看分叉回购。

### 基准测试结果

*   react-tracked-useTrackedState:`useTrackedState`在 react-tracked v0.4.0 中
*   react-tracked-useSelector: `useSelector`在 react-tracked v0.4.0 中
*   reactive-react-redux-useTrackedState:`useTrackedState`在 reactive-react-redux 4 . 0 . 0-beta 0 中
*   reactive-react-redux-use selector:`useSelector`在 reactive-react-redux 4 . 0 . 0-beta 0 中
*   react-redux-hooks: `useSelector`在 react-redux v7.1.0 中

[![screenshot1](img/e06170c380be5a22a4647c2d82b8484e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KkHaNL8Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.axlight.com/posts/benchmark-react-tracked/screenshot1.png)

[![screenshot2](img/4c3bea9a9dd7219b2f472a21c282a3b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--51VUBYIn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.axlight.com/posts/benchmark-react-tracked/screenshot2.png)

[![screenshot3](img/cfc5f2e4a930041c2866c56e7167450f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cncHF9Z7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.axlight.com/posts/benchmark-react-tracked/screenshot3.png)

### 期末备注

`useSelector`的基准代码在三个库中几乎是相同的。两个库之间的`useTrackedState`代码也是相同的。然而，`useSelector`的代码和`useTrackedState`的代码有点不同，它们是分开优化的。因此，在比较不同的挂钩时要注意。

我希望这些结果能鼓励开发人员尝试我的两个库。

* * *

最初发表于 2019 年 6 月 16 日[https://blog.axlight.com](https://blog.axlight.com/posts/benchmark-react-tracked/)。