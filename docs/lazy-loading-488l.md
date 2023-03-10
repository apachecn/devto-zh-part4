# 更懒

> 原文：<https://dev.to/chetchopra/lazy-loading-488l>

# 懒装

您是否希望在不影响用户体验的情况下提高应用程序的性能？

快速的页面加载对于 web 应用程序来说是绝对关键的。应用程序的初始加载时间会影响用户体验的方方面面。

-受众保留-如果他们会留下来
-受众转化-如果他们会回来
-整体用户体验

随着时间的推移，用户已经开始期待越来越丰富和互动的体验。他们想要更多的功能、更多的内容，而且想要更快。作为开发人员，这意味着更多的 Javascript，更多的来回发送的数据，但是当我们处理这么多数据时，我们怎么可能让我们的应用程序更快呢？尤其是当设备和网络条件不同时。

所以问题是我们想要更多、更快。但是一般来说，如果你想加载得更快，那么你只需要加载得更少。这完全是自相矛盾！
虽然大多数加速页面加载的策略包括减少初始有效载荷的大小，但这并不意味着你需要从你的应用中剥离功能和内容。

您可以通过简单地重新考虑什么是初始负载的绝对关键来实现。你真的需要一次性把所有东西都给用户吗？将非关键资源推迟到以后。

[![](img/3735773a31b14ef3704ac91d550f2f37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g5Lnnvgm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sazhbrwpcrgy8sr0hlrm.png)

[![](img/f3893ab8facf06585d6a45be2319839d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YuiZ0YHt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ya62okgywbirtsg0fual.png) 

因此，与其发送一个庞大的文件，不如试着将资源分开，这样你就可以按需交付。有几种方法可以做到这一点

-代码分割
-延迟加载图像和视频
-延迟加载应用数据

## 什么是懒加载

此时，您可能已经猜到了延迟加载是怎么回事。但是重申一下它背后的主要思想，延迟加载是在需要的时候加载内容，而不是一次全部加载。

这里有一个简单的例子来帮助巩固这个概念。

假设你正在访问 reddit/r/cats，当然有成千上万的猫图片/内容。如果 reddit 试图在你第一次访问该网站时向你发送所有这些信息，你可能要等很长时间才能看到你最喜欢的猫。相反，reddit 所做的是，当你第一次加载页面时，它只给你发送有限数量的猫，之后，随着你向下滚动，越来越多的猫被带进来。

## 上卷加载

这种惰性加载是通过监视滚动条的事件监听器实现的。当你点击页面底部时，事件触发，从而加载更多的猫，给用户一种能够无限向下滚动页面的感觉。

## 路口观察者

我们通过尝试只加载用户正在查看的内容来进一步实现只加载所需内容的想法。这种行为可以通过使用交叉点观察器来实现。例如，您可以在这里找到一个交叉点观察器 API。

交叉点观察器 API 让代码注册一个回调函数，每当它们希望监视的元素进入或退出视口时，或者当两者相交的量改变了所请求的量时，就执行该回调函数。

这意味着您需要为所有内容设置占位符，当视窗与占位符相交时，回调函数就会被触发。在这个回调中，您执行一个 fetch 来快速检索(通常)一个用来填充占位符的资源。在大多数情况下，获取一个资源比获取 10 个资源更快。

## 异步渲染

当一个组件正在加载或获取时，它的渲染被挂起。这意味着组件只有在准备好的时候才会出现。当它还没有准备好的时候，一个后备组件就取而代之。有多种方法可以实现这种行为。

-高阶组件

反应悬念

```
import React, { Component, lazy, Suspense } from 'react';
import './App.css';;
const MyComp = lazy(() => import('./components/myComp'));

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <div>another component</div>
          <Suspense fallback={<div>Loading.....</div>}>
            <MyComp />
          </Suspense>
        </header>
      </div>
    );
  }
}

export default App; 
```

```
import React from "react";

export default function myComp() {
  return <div>Hi there I am now loaded!</div>;
}; 
```

参考

YouTube 频道
tech sith-[https://www.youtube.com/watch?v=tV9gvls8IP8&list = LL-3 wvw 55 vza 7 tgx 28 xoow 1 q&index = 18&t = 288s](https://www.youtube.com/watch?v=tV9gvls8IP8&list=LL-3Wvw55vza7tgX28XooW1Q&index=18&t=288s)

极客为极客
T1】https://www.geeksforgeeks.org/what-is-lazy-loading/