# 为 Android Q 准备您的应用

> 原文：<https://dev.to/saurabh_arora90/preparing-your-app-for-android-q-2gk4>

我们进入了 Android 开发的第 10 个年头(Android Q 应该是 10.0)。根据 Beta 4，Android Q 的官方 API 级别为 29。尽管我们有 Beta 5，预计还有 Beta 6，但 API 已经被标记为最终版本，现在是一个很好的时间来看看 Android Q 将如何影响你的应用程序，以及应该做出哪些改变来完全支持 Android Q。

Android Q 中引入的重要变化(并非全部)可以分为两类:a)隐私&安全。b)用户体验

* * *

## 1)隐私&安全

#### a)后台活动开始

如果您尝试在应用程序处于后台时开始某项活动，将不再被允许。

**影响:**Q 上运行的所有 app(不考虑目标 SDK)。如果应用程序的目标是 Android Q，则会引发异常，而如果应用程序的目标不是 Android Q，而是在 Android Q 设备上运行，则活动不会启动。

**例外:**绑定服务，如可访问性、自动填充等。如果应用程序从系统接收到一个`PendingIntent`，我们可以用它来启动一个活动。如果该应用程序拥有`SYSTEM_ALERT_WINDOW`权限(在 Android GO 版本中已被移除)或该应用程序在最近的一次活动中被称为`finish()`(不建议依赖于此。“最近”的想法可能非常可疑)，那么您的应用程序也不受此限制。

**推荐方法:**通知触发活动