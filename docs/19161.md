# Ionic 3 中的分析:使用自定义装饰器轻松跟踪页面浏览量

> 原文：<https://dev.to/mahnuh/analytics-in-ionic-3-easily-track-page-views-with-a-custom-decorator-koi>

*本帖最初发布于[媒体](https://medium.com/prototype-berlin/analytics-in-ionic-3-easily-track-page-views-with-a-custom-decorator-33aa96e5a499)。*

如果你开发一个生产 Ionic 应用程序，你可能还想整合一些分析来分析你的应用程序是如何被使用的。有很多不同的选项可用，如谷歌分析，Firebase 或 Flurry 分析，但它们的共同点是:你必须实现跟踪你的个人视图。

我通常在我的应用程序中使用[@ ionic-native/Google-analytics](https://ionicframework.com/docs/native/google-analytics)，因为我真的不想手动将 this . Google analytics . track view(‘some page’)添加到每个页面，所以我尝试了不同的方法来实现自动视图跟踪。我最终使用的是一个自定义装饰器，我只需要将它添加到我想要跟踪的任何页面上。

## 这个装修工长什么样？

很高兴你问了。像这样: