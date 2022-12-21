# 角度性能:使用角度 CLI 的性能预算

> 原文：<https://dev.to/angular/angular-performance-performance-budgets-with-the-angular-cli-ggn>

*本帖最初发布于[https://justir . com/blog/2019/08/ng perf-setting-performance-budgets](https://juristr.com/blog/2019/08/ngperf-setting-performance-budgets)。前往[juristr.com/blog](https://juristr.com/blog)了解更多内容*

* * *

本文是我的“Angular Performance Week”系列的一部分，在该系列中，我每天都会发布一个基于 web.dev/angular.性能文章的新视频。本文重点介绍我们如何使用 Angular CLI 定义性能预算。

> 注:这篇文章和所附的书呆子视频课是基于[明科·格切夫](https://twitter.com/mgechev)和[斯蒂芬·弗鲁恩](https://twitter.com/stephenfluin)关于[web.dev/angular](https://web.dev/angular)的文章。完全归功于他们👍

## 角度性能系列

1.  [路由级代码拆分](https://juristr.com/blog/2019/08/ngperf-route-level-code-splitting/)
2.  [预载角度懒惰路线](https://juristr.com/blog/2019/08/ngperf-preloading-lazy-routes)
3.  **使用 Angular CLI 的性能预算**
4.  [优化角度变化检测](https://juristr.com/blog/2019/08/ngperf-optimize-change-detection/)
5.  [使用 CDK 虚拟滚动大列表](https://juristr.com/blog/2019/08/ngperf-virtual-scrolling-cdk/)
6.  [与角度维修工人一起进行预加工](https://juristr.com/blog/2019/08/ngperf-precaching-serviceworker)

订阅我的简讯，不要错过其他视频[。](https://juristr.com/newsletter)

## 配置绩效预算

衡量是非常重要的，没有数字我们就不知道潜在的问题，我们也没有一个可以改进的基础。[性能预算](https://web.dev/performance-budgets-101)是对可能影响我们应用性能的指标进行限制的一种方式。在本课中，我们将学习如何配置 Angular CLI 来配置性能预算、监控性能预算并在超出预算时发出警报。

[https://www.youtube.com/embed/xcV813dYEb0](https://www.youtube.com/embed/xcV813dYEb0)

### 原创 web.dev 文章

想阅读 web.dev 的原始文章吗？点击这里查看！。