# React lazy 函数是如何实现的？焦虑...会很短😉

> 原文：<https://dev.to/mfrachet/how-is-the-react-lazy-function-implemented-suspense-it-gonna-be-short-4pkn>

React 的团队引入悬念和并发渲染已经有一段时间了。有了钩子，React 提供了新的惊人特性。

我们“能够”(至少在开发模式下)做到:

*   创建管理异步操作特定优先级的应用程序
*   像管理同步计算一样管理异步计算
*   到处使用功能组件而不是类

我对 React 的未来感到非常兴奋！

* * *

今天，我想谈谈`Suspense`的一个特性，即 [React v16.6](https://reactjs.org/blog/2018/10/23/react-v-16-6.html) 中引入的`lazy`功能。

该函数旨在提供一种简单的方法来依靠 bundler 的代码分割，使用一些代码，如:

```
import React, { lazy, Suspense } from 'react';

const LazyComponent = lazy(() => import('./components/myComponent'));

const App = () => (
  <Suspense fallback={<div>Waiting...</div>}>
    <LazyComponent />
  </Suspense>
) 
```

Enter fullscreen mode Exit fullscreen mode

## 什么那个？..

一开始可能会有点困扰，我们该如何:

*   代码分割我们的代码，这是一个构建时特性
*   进行创建组件的异步计算
*   使用一个(异步？)旨在同步的渲染函数中的组件

使用 2-3 行？...！

[![What the?..](img/33ffbb65ef14c1377e405abd3094f402.png)](https://i.giphy.com/media/Vfie0DJryAde8/giphy.gif)

## 悬念...🤯

这不是*那个*的魔法，能够存在多亏了`Suspense`这个组件。

这个组件有点特别，每当你在它的一个子组件中`throw`一个`Promise`，它就会`catch`承诺、解析它并重新渲染它的子组件。

您知道在 JavaScript 中除了错误还能抛出其他东西吗？！

这就是它被称为`Suspense`的原因:由于关键字`throw`，它*暂停*应用程序的正常执行流程，并在“恢复”它之前进行一些特定的计算。它不会在你的代码的确切位置恢复它，但至少，它重新呈现了它的子节点**，这让你感觉好像回到了原来的执行位置**。

我试着在[这篇媒体文章](https://medium.com/free-code-camp/react-cache-time-slicing-and-fetching-with-a-synchronous-api-2a57dc9c2e6d)中写下它，但是没有成功——我当时的想法没有那么有条理。

我现在不保留“悬念”，所以让我们检查一下我遇到的`lazy`函数的*一个*实现:

```
import React from "react";

let IDS = 0;
const loaded = {};

export const lazy = modulePathResolver => {
  const id = IDS++;

  return props => {
    const LoadedComponent = loaded[id];

    if (LoadedComponent) {
      return <LoadedComponent {...props} />;
    }

    throw modulePathResolver().then(lazyModule => {
      const Component = lazyModule.default;
      loaded[id] = Component;
    });
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

**[想玩的话 github gist 上有。](https://gist.github.com/mfrachet/a04cc57a500de85170e2ade4b9406305)T3】**

显然，只有当组件在`Suspense`父组件中使用时，这个代码片段才起作用。

`lazy`函数接受一个参数`modulePathResolver`，它是一个`Promise`，解析包含您的*懒惰*组件的模块。

`lazy`函数返回一个实际上是(功能)组件的函数。所有对`id`的引用只是为了确保组件只被加载一次。

如果你仔细看看代码，它看起来真的像一个缓存系统，但不是直接设置缓存值，而是包装缓存设置的承诺，以便悬念父级可以解决它，*懒洋洋地*。

你知道吗？由于这是一个异步操作，它可能需要一些时间来执行，几毫秒、几秒甚至几分钟。在异步解析期间*显示的是什么？显示`Suspense`组件的`fallback`道具！仅此而已！*

## 而现在，什么？

您已经知道了`lazy`函数的工作方式，但是您现在也知道了`Suspense`是如何工作的。你现在可以想象各种异步解决方案，而不用随时随地创建`isLoading`状态。低-高质量图片的惰性图像加载怎么办😉？

贾里德·帕尔默非常支持这一点，他已经在多次演讲中谈到了这一点，比如在 2019 年连锁反应大会上的一次演讲

我对这个特性的观点是，它也将我们用来进行的副作用计算推到了应用程序的边缘。我们可以毫无困难地使用同步 API 来制作异步的东西。这让我想到了单子，以及用琐碎的代码隔离和组合导致(副作用)的原因的能力。

这不是很酷吗？！

大家编码快乐！React 将迎来美好的一天！🚀