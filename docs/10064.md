# 我自己的安卓库？

> 原文：<https://dev.to/_nedimf/my-own-android-library-3n3j>

### 话题介绍

我做 android 开发已经一年半了。我一直在使用不同的第三库制作一个又一个应用程序，以实现我的最终目标，并获得各种各样的应用程序功能。在我为客户开发的最新安卓应用中，我几乎没有想到什么。如果我想改变库的行为并向其中添加我的函数，该怎么办？然后会发生什么？

### 尤里卡零件

如果你有很好的 Java 或 Kotlin 图形，这将很容易跟随。如果没有，那就去开发更多的应用程序，并且做得更好。然后回到这里，我保证我们不会逃跑。

我们需要改变一件事，或者库真的很棒，但有点问题，我们正在修复它。第一步检查想要的库是否是开源的。开源意味着我们可以用它做任何我们想做的事情，当然是在编码方面。

#### 跟随低谷

我们需要采取什么步骤来获得我们自己版本的库。

*   派生您想要更改的现有项目或克隆项目，并将其推送到 Bitbucket 或 Github 上的新存储库
*   转到您想要更改的文件，在我们的示例中，我们只想更改库的 res 值。
*   保存更改
*   转到 [JitPack](https://jitpack.io) 并使用您的 Github 帐户登录。
*   将您的项目名称添加到查找点[用户名]/[名称库]
*   获取版本并点击获取按钮

现在你有了所有可以使用的部分，并在你的应用中实现它

### 最终结果

*   在你的 app **build.gradle** 中添加 [JitPack](https://jitpack.io)

```
 allprojects {
        repositories {
            ...
            maven { url 'https://jitpack.io' }
        }
    } 
```

*   在应用程序中实施

```
dependencies {
            implementation 'com.github.username:LibName:Tag'
    } 
```

来源[nedim.co](https://nedim.co/article/id/bm68aj3665ykkllh2b1c)