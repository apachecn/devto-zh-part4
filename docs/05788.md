# 角度性能:预加载惰性路线

> 原文：<https://dev.to/angular/angular-performance-preloading-lazy-routes-16m2>

*本帖最初发布于[https://justir . com/blog/2019/08/ng perf-preloading-lazy-routes](https://juristr.com/blog/2019/08/ngperf-preloading-lazy-routes)。前往[juristr.com/blog](https://juristr.com/blog)了解更多内容*

* * *

这篇文章是我的“Angular Performance Week”系列的一部分，在那里我每天都会发布一个基于 web.dev/angular.上的性能文章的新视频。今天我们将看看在 Angular 中预加载惰性路线的各种方法:预加载所有模块，自定义预加载以及使用 [ngx-quicklink](https://github.com/mgechev/ngx-quicklink) 。

> 注:这篇文章和所附的书呆子视频课是基于[明科·格切夫](https://twitter.com/mgechev)和[斯蒂芬·弗鲁恩](https://twitter.com/stephenfluin)关于[web.dev/angular](https://web.dev/angular)的文章。完全归功于他们👍

## 角度性能系列

1.  [路由级代码拆分](https://juristr.com/blog/2019/08/ngperf-route-level-code-splitting/)
2.  **预载角度懒惰路线**
3.  [使用 Angular CLI 的性能预算](https://juristr.com/blog/2019/08/ngperf-setting-performance-budgets/)
4.  [优化角度变化检测](https://juristr.com/blog/2019/08/ngperf-optimize-change-detection/)
5.  [使用 CDK 虚拟滚动大列表](https://juristr.com/blog/2019/08/ngperf-virtual-scrolling-cdk/)
6.  [与角度维修工人一起进行预加工](https://juristr.com/blog/2019/08/ngperf-precaching-serviceworker)

订阅我的简讯，不要错过其他视频[。](https://juristr.com/newsletter)

## 预加载所有模块

在[之前的文章](https://juristr.com/blog/2019/08/ngperf-route-level-code-splitting)中，我们讨论了使用 Angular CLI 延迟加载角度路由。当你延迟加载一个模块时，用户可以从应用程序的快速加载中获益。然而，这是以模块在运行时加载的延迟为代价的。为了缓解这个问题，我们可以应用一些预加载策略。Angular 附带了一个默认的，预加载所有懒惰的模块。让我们看看如何实现它。

[https://www.youtube.com/embed/0kU-nC3umHQ](https://www.youtube.com/embed/0kU-nC3umHQ)

## 自定义预加载策略

预加载所有模块是一种非常极端的方法，并不总是可取的。例如，您不希望预加载用户可能甚至无法访问的惰性路由。因此，在这一课中，我们将了解如何在 Angular 中定义自定义预加载策略。

[https://www.youtube.com/embed/ZGyTnHm_fa8](https://www.youtube.com/embed/ZGyTnHm_fa8)

## 用 ngx-quicklink 预加载

在上一课[中，我们学习了如何实施定制预加载策略](https://egghead.io/lessons/egghead-implement-a-custom-preloading-strategy-with-angular)。无论是基于用户的权限还是一些运行时应用程序配置，这都给了你很多控制预加载和不预加载哪个路由的权力。在这一课中，我们使用 [ngx-quicklink](https://github.com/mgechev/ngx-quicklink) ，这是一个库，通过自动加载页面上所有可见的链接，极大地简化了定制预加载。

[https://www.youtube.com/embed/fZAozYgYzhI](https://www.youtube.com/embed/fZAozYgYzhI)

### 原创 web.dev 文章

想阅读 web.dev 的原始文章吗？点击这里查看！。