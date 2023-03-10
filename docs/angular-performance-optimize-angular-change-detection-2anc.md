# 角度性能:优化角度变化检测

> 原文：<https://dev.to/angular/angular-performance-optimize-angular-change-detection-2anc>

*本帖最初发布于[https://justir . com/blog/2019/08/ng perf-optimize-change-detection](https://juristr.com/blog/2019/08/ngperf-optimize-change-detection)。前往[juristr.com/blog](https://juristr.com/blog)了解更多内容*

* * *

这篇文章是我的“Angular Performance Week”系列的一部分，在那里我每天发布一个基于 web.dev/angular. Angular 的性能文章的新视频，Angular 很快，但我们可以让它更快。一种方法是调整它的变化检测系统。让我们看看怎么做。

> 注:这篇文章和所附的书呆子视频课是基于[明科·格切夫](https://twitter.com/mgechev)和[斯蒂芬·弗鲁恩](https://twitter.com/stephenfluin)关于[web.dev/angular](https://web.dev/angular)的文章。完全归功于他们👍

## 角度性能系列

1.  [路由级代码拆分](https://juristr.com/blog/2019/08/ngperf-route-level-code-splitting/)
2.  [预载角度懒惰路线](https://juristr.com/blog/2019/08/ngperf-preloading-lazy-routes)
3.  [使用 Angular CLI 的性能预算](https://juristr.com/blog/2019/08/ngperf-setting-performance-budgets)
4.  **优化角度变化检测**
5.  [使用 CDK 虚拟滚动大列表](https://juristr.com/blog/2019/08/ngperf-virtual-scrolling-cdk/)
6.  [与角度维修工人一起进行预加工](https://juristr.com/blog/2019/08/ngperf-precaching-serviceworker)

订阅我的简讯，不要错过其他视频[。](https://juristr.com/newsletter)

## 优化角度变化检测

变化检测是 Angular 背后的魔力，它可以自动识别何时发生变化。这要么是由于手动触发，要么是通过异步事件。一旦检测到变化，它将遍历各种角度分量并触发刷新。然而，通常它非常快——尤其是在较大的应用程序中——它可能会触发大量的计算，从而阻塞主浏览器线程。在这一课中，我们将学习如何通过**减少更新**所需的组件数量，并通过**纯管道**来优化 Angular 的变化检测机制。

[https://www.youtube.com/embed/Ki0V0DPCrzQ](https://www.youtube.com/embed/Ki0V0DPCrzQ)

### 原创 web.dev 文章

想阅读 web.dev 的原始文章吗？点击这里查看！。