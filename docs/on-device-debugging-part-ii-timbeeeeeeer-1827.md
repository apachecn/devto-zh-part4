# 设备上调试第二部分:Timbeeeeeeer！

> 原文：<https://dev.to/zmdominguez/on-device-debugging-part-ii-timbeeeeeeer-1827>

在过去的一年里，我的团队一直在为我们的应用程序构建一个开发者选项屏幕。这是一个简单的 [`PreferenceScreen`](https://developer.android.com/reference/androidx/preference/PreferenceScreen.html) ，可以在调试版本中获得，旨在帮助我们:

*   不需要连接到电脑就能知道发生了什么
*   无需重新安装即可测试各种配置
*   主持我们正在尝试探索的各种实验

在这一系列的帖子中，我将分享这些不同的选项是什么，以及我们是如何做出它们的。

(如果你不熟悉 [`PreferenceFragmentCompat`](https://developer.android.com/reference/kotlin/androidx/preference/PreferenceFragmentCompat.html) ，我强烈建议在继续之前先阅读一下。你可以从这个 [AndroidX 指南](https://developer.android.com/guide/topics/ui/settings.html)开始设置。)

看第一部分(现在开，现在关)[这里](https://zarah.dev/2019/06/22/debug-options-toggles.html)。

* * *

我们在 Woolworths 经常使用的工具之一是[木材](https://github.com/JakeWharton/timber)。我们不仅使用它来帮助我们在开发过程中进行调试，还在测试过程中尽早发现问题，并关注生产中的情况。

以前，当我们有一个用于测试的构建并且事情没有按计划进行时，我们会收到一个类似这样的截图:

<center>
[![](img/5714fa86e770c6a57a9dea0bfbe77e64.png "source: imgur.com")](https://imgur.com/Bzleb1m)

<small>Uh 🙀</small>
</center>

我们试图找出他们在应用程序崩溃前试图做的事情，复制他们之前的确切情况，并试了又试。更多的时候，我们撞上了“那不应该发生”的墙——尽管它显然已经发生了。🤦‍♀️

在这种情况下，我们求助于木材。我们记录下这些“我们不应该来这里！！!"使用`Timber.e`的场景。在生产中，这些日志通过`Crashlytics.log()`发送到 Fabric。在调试版本中，这些日志被发送到崩溃日志屏幕。

这个屏幕显示了 stacktrace(如果有的话),以及一些关于构建和设备的基本信息。我们还决定使用这个屏幕来记录任何未捕获的异常。屏幕底部有一个按钮，通过[意图选择器](https://developer.android.com/training/sharing/send)发送崩溃日志。

<center>
[![](img/61e2af1024e3c943f9c8d8876aded7ed.png "source: imgur.com")](https://imgur.com/StEAFvr)

<small>Crash log screen variations</small>
</center>

为了实现这一点，我们覆盖了`Timber`的`e` :

```
class LoggingTree(private val app: Application) : Timber.DebugTree() {

    override fun e(message: String, vararg args: Any) {
        super.e(message, *args)
        printStackTrace(null, message, *args)
    }

    override fun e(t: Throwable, message: String?, vararg args: Any) {
        super.e(t, message, *args)
        printStackTrace(t, message, *args)
    } 
```

并且`printStackTrace`简单地将信息传递给显示错误的专用`Activity`。

我们用`CrashType` :
作为额外的`Intent`来提供这种错误

```
enum class LogType(val additionalMessage: String, @ColorRes val background: Int) {
    UNCAUGHT_EXCEPTION("!!! DO NOT IGNORE! THIS WILL CAUSE A CRASH IN PRODUCTION !!!", R.color.error_color),

    LOG("We are monitoring an issue. You may still send this log if you want.", R.color.white);
} 
```

崩溃屏幕的实现非常简单，我们检索额外的内容，并使用数据绑定来显示消息和样式化标题。(出于好奇，我之前写过关于在数据绑定中使用资源引用的

因为我们也使用崩溃日志屏幕来处理未捕获的异常，所以当出现问题时，我们直接调用`Activity`。这意味着它跳过了`Timber`处理，但是有时在 Logcat 中查看日志仍然是有用的，所以我们也在那里记录 stack trace:

```
@SuppressLint("LogNotTimber")
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    // ...

    // We still want to display the stack trace in Logcat for uncaught exceptions
    // but are not using Timber.e here lest we get into an infinite loop
    Log.e("FIXME", Log.getStackTraceString(stackTrace))

} 
```

屏幕底部的“发送崩溃”按钮发出一个`ACTION_SEND` `Intent`，这样用户就可以通过电子邮件、Slack 或他们选择的任何其他应用程序发送信息(我也在这里写了那个！).

这似乎是一件简单的事情，但它一次又一次地帮助我们以更有意义的方式快速解决问题。即使那些帮助我们测试的人选择发送截图，也有更多的相关信息，而不是通用的崩溃对话框。💞