# Angular 8 的差分加载指南

> 原文：<https://dev.to/auth0/a-guide-to-angular-8-s-differential-loading-2p54>

**TL；DR:** 角 8 来了！了解它最酷的新特性之一:差异装载。差异加载让您可以为不同的浏览器提供不同的包，让您的应用程序更快！

在我写这篇文章的时候，Angular 8 才发布了大约一周，但已经有 17，000 篇“新内容”文章发表了。我不会把我自己的观点放在那堆东西上，我会让你参考官方的 Angular 发布公告，但这里有一些亮点:

*   不，Ivy 还没有准备好(这是一个选择加入的预览)。
*   不， [Bazel](https://bazel.build/) 还没有准备好(这是一个选择加入的预览)。
*   构建器允许您扩展和定制 CLI。例如，您现在能够从 CLI 将[部署到 Firebase](https://github.com/angular/angularfire2/pull/2046) 和其他提供商。
*   对 web workers 的支持[有所改进，比如从 CLI 生成它们并在您的应用程序中使用它们的能力。](https://v8.angular.io/guide/web-worker)
*   您可以使用[标准的`import()`语法](https://next.angular.io/guide/deprecations#loadchildren-string-syntax)，而不是使用 Angular 特有的“神奇字符串”语法来进行惰性加载。您甚至可以使用 [`angular-lazy-routes-fix`](https://github.com/phenomnomnominal/angular-lazy-routes-fix) 工具为您的应用程序自动执行此操作。
*   新的[统一位置服务](https://v8.angular.io/guide/upgrade#using-the-unified-angular-location-service)改进了从 AngularJS `$location`服务的迁移。
*   Angular 团队创建了一个简化的[入门指南](https://next.angular.io/getting-started)。
*   有一个新的[弃用指南](https://v8.angular.io/guide/deprecations)来帮助用户更新 Angular。
*   [CLI 中默认开启差分加载](https://v8.angular.io/guide/deployment#differential-loading)。

在本文中，我想深入讨论最后一个问题:差异加载。那是什么？为什么重要？我需要做些什么(如果有的话)？

[继续阅读](https://auth0.com/blog/angular-8-differential-loading/)📖

[![Angular illustration](img/4d397c3d91824a2ac44a31f4861672c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uaQjgdHk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.auth0.com/blog/illustrations/angular.png)