# 角度性能:CDK 虚拟滚动

> 原文：<https://dev.to/angular/angular-performance-virtual-scrolling-with-the-cdk-50pl>

*本帖最初发表于[https://justir . com/blog/2019/08/ng perf-virtual-scrolling-CDK](https://juristr.com/blog/2019/08/ngperf-virtual-scrolling-cdk)。前往[juristr.com/blog](https://juristr.com/blog)了解更多内容*

* * *

这篇文章是我的“角度表现周”系列的一部分，在那里我每天发布一个基于 web.dev/angular.表现文章的新视频

> 注:这篇文章和所附的书呆子视频课是基于[明科·格切夫](https://twitter.com/mgechev)和[斯蒂芬·弗鲁恩](https://twitter.com/stephenfluin)关于[web.dev/angular](https://web.dev/angular)的文章。完全归功于他们👍

## 角度性能系列

1.  [路由级代码拆分](https://juristr.com/blog/2019/08/ngperf-route-level-code-splitting/)
2.  [预载角度懒惰路线](https://juristr.com/blog/2019/08/ngperf-preloading-lazy-routes)
3.  [使用 Angular CLI 的性能预算](https://juristr.com/blog/2019/08/ngperf-setting-performance-budgets)
4.  [优化角度变化检测](https://juristr.com/blog/2019/08/ngperf-optimize-change-detection)
5.  **使用 CDK 虚拟滚动大列表**
6.  [与角度维修工人一起进行预加工](https://juristr.com/blog/2019/08/ngperf-precaching-serviceworker)

订阅我的简讯，不要错过其他视频[。](https://juristr.com/newsletter)

## 利用角度 CDK 实现虚拟滚动

浏览器每天都在变得更快，但是，DOM 更新仍然是昂贵的。如果我们有大的列表，浏览器需要提前呈现它们，创建成百上千的 DOM 节点，即使用户可能不会在列表中向下滚动那么远。这不仅降低了滚动本身的速度，甚至我们应用程序的页面负载也会受到影响。为了优化这种体验，我们可以使用**虚拟滚动**，特别是在这一课中，我们使用 Angular 的 CDK 来实现这一点。

[https://www.youtube.com/embed/5L4YTcSrRXs](https://www.youtube.com/embed/5L4YTcSrRXs)

### 原创 web.dev 文章

想阅读 web.dev 的原始文章吗？点击这里查看！。