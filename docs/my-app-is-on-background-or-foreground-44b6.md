# 我的应用程序在后台还是前台？

> 原文：<https://dev.to/ddinorahtovar/my-app-is-on-background-or-foreground-44b6>

使用 LifecycleObserver 解决 OnPause & OnResume 的问题

StackOverflow 上问得最多的问题之一是“我如何知道我的应用程序是否在后台”，我们有多个副本，如[这个](https://stackoverflow.com/questions/3667022/checking-if-an-android-application-is-running-in-the-background)或[另一个](https://stackoverflow.com/questions/2166961/determining-the-current-foreground-application-from-a-background-task-or-service)。
这个问题长期以来一直困扰着许多 android 开发者，一段时间以来，Android 开发者一直在这样做: