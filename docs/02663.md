# 角度性能:与维修工人一起预加工

> 原文：<https://dev.to/angular/angular-performance-precaching-with-the-service-worker-4lg5>

*本帖最初发布于[https://justir . com/blog/2019/08/ng perf-pre caching-service worker](https://juristr.com/blog/2019/08/ngperf-precaching-serviceworker)。前往[juristr.com/blog](https://juristr.com/blog)了解更多内容*

* * *

这篇文章是我的“Angular Performance Week”系列的一部分，在这个系列中，我每天都会发布一个新的视频，该视频基于《今日 web.dev/angular.》上的性能文章。我们正在深入研究服务工人 API，具体来说就是利用 Angular 的集成服务工人包。

> 注:这篇文章和所附的书呆子视频课是基于[明科·格切夫](https://twitter.com/mgechev)和[斯蒂芬·弗鲁恩](https://twitter.com/stephenfluin)关于[web.dev/angular](https://web.dev/angular)的文章。完全归功于他们👍

## 角度性能系列

1.  [路由级代码拆分](https://juristr.com/blog/2019/08/ngperf-route-level-code-splitting/)
2.  [预载角度懒惰路线](https://juristr.com/blog/2019/08/ngperf-preloading-lazy-routes)
3.  [使用 Angular CLI 的性能预算](https://juristr.com/blog/2019/08/ngperf-setting-performance-budgets)
4.  [优化角度变化检测](https://juristr.com/blog/2019/08/ngperf-optimize-change-detection)
5.  [使用 CDK 虚拟滚动大列表](https://juristr.com/blog/2019/08/ngperf-virtual-scrolling-cdk)
6.  **与角度维修工人一起进行预加工**

订阅我的简讯，不要错过其他视频[。](https://juristr.com/newsletter)

## 用 Angular 的服务工作者包缓存

为了进一步加快我们的应用程序，并在不稳定的网络条件下提供更愉快的体验，我们可能希望使用一个允许我们应用预缓存机制的服务人员。幸运的是，Angular 团队已经提供了一个与框架很好集成的服务工作者模块。让我们看看如何将它应用到我们的应用程序中。

[https://www.youtube.com/embed/2-SoCdPo4l8](https://www.youtube.com/embed/2-SoCdPo4l8)

### 原创 web.dev 文章

想阅读 web.dev 的原始文章吗？点击这里查看！。