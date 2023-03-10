# Kotlin 多平台在不到五分钟的时间内完成两个应用

> 原文：<https://dev.to/benwhitley/two-apps-in-less-than-five-minutes-with-kotlin-multiplatform-1gm1>

如果你正在读这篇文章，你可能听说过很多关于 Kotlin 多平台的事情，但是不知道从哪里开始。该平台发展迅速，这意味着许多文档已经过时，大多数教程似乎都是带你从头开始设置项目和工具。但是没有人喜欢看说明书！在不到五分钟的时间里，我们将带您完成一些共享代码的设置。

* * *

先决条件:我们将作弊，不启动时钟，直到你安装了 Xcode 10.3+和 Android Studio 3.4.2，并安装了一个模拟器，并为 Android 运行。对于 Xcode，在 Mac App Store [这里](https://apps.apple.com/us/app/xcode/id497799835?mt=12)找到并安装它。苹果电脑上没有？嗯，这些都不行(你需要一台 Mac)。

*对于 Android Studio，点击[此处](https://developer.android.com/studio)获取应用，按照说明[此处](https://developer.android.com/studio/install)进行设置，按照说明设置 Android 模拟器[此处](https://developer.android.com/studio/run/emulator)。准备好了吗？好，现在开始计时。*

* * *

克隆这个库并在 Android Studio 中打开它。该项目目前包含两个独立的 Hello World 应用程序:Android 和 iOS 各一个。运行 Android 应用程序，看到它显示“你好，Android 世界！”

现在，在 iOS 模拟器中打开`/iosApp/iosApp.xcworkspace`并运行`iosApp`可以看到它显示“你好，iOS 世界！”虽然它们共享一个文件夹结构，但这些 iOS 和 Android 应用程序是相互独立的。现在我们将使用 Kotlin 多平台将它们连接在一起。

首先打开`/app/src/commonMain/kotlin/sample/Sample.kt`，里面有如下常用代码:

```
class Greeting {
    fun hello(): String = "Hello, Android and iOS worlds!"
} 
```

接下来，在 Android Studio 中打开`/app/src/main/java/sample/SampleAndroid.kt`。它包含了 Android 版本的 Hello World 应用程序。为了使它成为一个 KMP 应用程序，替换分配给`textView.text`的值——当前是“你好，安卓世界！”——并将其替换为`Greeting().hello()`。

最后回到 Xcode，打开`ViewController.swift`。在`import UIKit`下面，加上`import app`，这是 KMP 图书馆的名字。然后，就像在 Android 应用程序中一样，将分配给`label.text`的值从“Hello，iOS world！”去`Greeting().hello()`。重新运行这两个应用程序，看到它们都显示相同的字符串！

“你好，安卓和 iOS 世界！”

全部完成！停止计时！您现在是 Kotlin 多平台开发人员了！

有哪些好的后续步骤？尝试使用`expect` / `actual`语句调用一些本机代码，然后添加一个现有的多平台库，如 Ktor，并进行一些联网。

如果你来自 iOS 背景，想要更深入地了解如何使用 Kotlin 多平台来制作一个更复杂的应用程序，那么你可能想看看我写的这篇文章。

如您所见，Kotlin 多平台的一切都是从小事做起。我们最近推出了 Touchlab RefactoryKMP 提供的增量重构服务，一次只关注一个特性，不会中断您当前的开发工作流程。点击了解更多[。](http://touchlab.co/refactory)