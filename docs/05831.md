# 角度性能:路线级别代码分割

> 原文：<https://dev.to/angular/angular-performance-route-level-code-splitting-2gfc>

*本帖最初发布于[https://justir . com/blog/2019/08/ng perf-route-level-code-splitting](https://juristr.com/blog/2019/08/ngperf-route-level-code-splitting)。前往[juristr.com/blog](https://juristr.com/blog)了解更多内容*

* * *

本文是我的“Angular Performance Week”系列的一部分，在该系列中，我每天都会发布一个基于《今日 web.dev/angular.》上的性能文章的新视频。我们来看看路由级代码拆分，以提高应用的 TTI。

> 注:这篇文章和所附的书呆子视频课是基于[明科·格切夫](https://twitter.com/mgechev)和[斯蒂芬·弗鲁恩](https://twitter.com/stephenfluin)关于[web.dev/angular](https://web.dev/angular)的文章。完全归功于他们👍

## 角度性能系列

1.  **路由级代码拆分**
2.  [预载角度懒惰路线](https://juristr.com/blog/2019/08/ngperf-preloading-lazy-routes)
3.  [使用 Angular CLI 的性能预算](https://juristr.com/blog/2019/08/ngperf-setting-performance-budgets/)
4.  [优化角度变化检测](https://juristr.com/blog/2019/08/ngperf-optimize-change-detection/)
5.  [使用 CDK 虚拟滚动大列表](https://juristr.com/blog/2019/08/ngperf-virtual-scrolling-cdk/)
6.  [与角度维修工人一起进行预加工](https://juristr.com/blog/2019/08/ngperf-precaching-serviceworker)

订阅我的简讯，不要错过其他视频[。](https://juristr.com/newsletter)

## 路由级代码拆分

TTI(互动时间)是一个有趣的指标，衡量你的应用程序需要多长时间来响应，用户可以互动。这是一个很好的性能指标。提高 TTI 的一个方法是只加载应用程序中最少的必要部分，然后在需要的时候“延迟加载”其他部分。

在这节视频课中，我们将一步一步地实现路由级代码拆分和延迟加载。

[https://www.youtube.com/embed/gxlCl5P_PC8](https://www.youtube.com/embed/gxlCl5P_PC8)

### 奖励:显示惰性路线的装载指示器

[https://www.youtube.com/embed/C0FBR4EUqgk](https://www.youtube.com/embed/C0FBR4EUqgk)

### 原创 web.dev 文章

想阅读 web.dev 的原始文章吗？点击这里查看！。