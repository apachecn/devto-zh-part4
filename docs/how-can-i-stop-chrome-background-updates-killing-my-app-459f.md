# 如何阻止 Chrome 后台更新杀死我的应用？

> 原文：<https://dev.to/jimmydeemo/how-can-i-stop-chrome-background-updates-killing-my-app-459f>

那好吧。关于 DEV 的第一个帖子，有点像求救。有人能帮我分析一下这个问题吗？看起来我们(和其他人)有这样的例子，当 Chrome 更新时，依赖 Chrome 的网页浏览(大部分来自第三方插件)导致应用程序被杀死。然而，我几乎找不到任何关于这个过程或如何潜在地移植它的信息。有人能帮忙吗？

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [如何阻止 Chrome 后台更新查杀我的 app？](https://stackoverflow.com/questions/52553558/how-can-i-stop-chrome-background-updates-killing-my-app)

Sep 28 '18 Comments: 4 Answers: 1[![](img/e3f0373ec76330150a340eacd410b600.png)8![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/52553558/how-can-i-stop-chrome-background-updates-killing-my-app) </header>

**更新后的帖子**

***问题***

我们有各种日志表明 Chrome 的后台更新正在扼杀我们使用 webviews 的应用程序。日志都遵循相似的模式；

```
3034  3049 I ActivityManager: Force stopping com.android.chrome appid=10115 user=-1: installPackageLI
3034  3049 I ActivityManager: Killing 963:com.google.android.googlequicksearchbox:search/u0a80 (adj 500): stop com.android.chrome,installPackageLI
3034
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/52553558/how-can-i-stop-chrome-background-updates-killing-my-app)</button>