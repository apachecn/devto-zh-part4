# 第 4 天:Kotlin 协同程序和应用程序测试

> 原文：<https://dev.to/rahulchowdhury/day-4-kotlin-coroutines-and-app-testing-3i3d>

哟，这是我旅程的第四天。

# 更新到现在

#### 昨天

致力于设置本地持久性，使我的应用程序可以离线访问(可能有陈旧的数据，但仍然)。

我使用空间来离线存储数据，我必须说使用它是一种享受。

当我第一次开始开发 Android 应用程序时，我记得我必须处理讨厌的光标实现来从 SQLite 存储/获取数据。

然后，Realm 出现了，使得存储本地数据变得容易多了。

房间在另一层。在 SQLite 上使用 ORM 的好处和其他好处一样，比如:

*   编译时查询验证

*   对`LiveData`和 Kotlin 协程的一流支持

#### 今日

我花了一上午的时间学习 Kotlin 协程，以及它们对 Android 应用程序的用处。

我记得我第一次接触反应式编程的时候。Android 上的 RxJava 看起来很神奇。

科特林的协程比那个略胜一筹。使用协程绝对是一种享受，主要是因为流行的库如 Retrofit 和 Room 支持开箱即用。

此外，使用`viewModelScope`运行协程意味着当用户离开屏幕时，您不必担心取消协程。他们启动的任何子协程都与父协程一起被取消。

你可以在这里了解更多:[https://medium . com/Android developers/coroutines-on-Android-part-I-getting-the-background-3e 0 e 54d 20 bb](https://medium.com/androiddevelopers/coroutines-on-android-part-i-getting-the-background-3e0e54d20bb)

# 接下来是什么？

我需要创建一个绑定适配器，允许 Glide 使用数据绑定将图像加载到我的`ImageView`中。

之后，我的主要精力将放在学习如何在 Android 上进行自动化测试。

我的计划是:

*   从单元测试视图模型开始
*   存储库的检测测试进展
*   完成控制器的 UI 测试(活动、片段等)

让我们看看我今天能做多少。

被卖方收回的汽车👉🏻[https://github . com/rahulchowdhury/elly/tree/add-elephant-profile](https://github.com/rahulchowdhury/elly/tree/add-elephant-profile)