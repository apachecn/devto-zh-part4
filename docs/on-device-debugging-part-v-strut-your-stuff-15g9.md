# 设备上调试第五部分:展示你的东西

> 原文：<https://dev.to/zmdominguez/on-device-debugging-part-v-strut-your-stuff-15g9>

在过去的一年里，我的团队一直在为我们的应用程序构建一个开发者选项屏幕。它是一个简单的 [`PreferenceScreen`](https://developer.android.com/reference/androidx/preference/PreferenceScreen.html) ，可以在调试构建中帮助我们:

*   不需要连接到电脑就能知道发生了什么
*   无需重新安装即可测试各种配置
*   主持我们正在尝试探索的各种实验

在这一系列的帖子中，我将分享这些不同的选项是什么，以及我们是如何做出它们的。

阅读本系列的其他文章:

*   第一部分:现在开了，现在关了
*   第二部分:Timbeeeeeeer！
*   [第三部分:检查、复位、重复](https://zarah.dev/2019/07/01/debug-options-info.html)
*   第四部分:记录所有的事情！

(如果你不熟悉 [`PreferenceFragmentCompat`](https://developer.android.com/reference/kotlin/androidx/preference/PreferenceFragmentCompat.html) ，我强烈建议在继续之前先阅读一下。你可以从这个 [AndroidX 指南](https://developer.android.com/guide/topics/ui/settings.html)开始设置。)

* * *

开发应用程序意味着我们不断调整事物的外观。改变这个绿色阴影，用这个小部件代替那个，把这个东西移到那里。

在我们的设备上调试系列的最后一部分，我们来看一些展示我们的应用程序应该是什么样子的方法。

<center>
[![](img/32ceecb50c709c857938614f57ccd9a6.png "source: imgur.com")](https://imgur.com/mOr5Aou)

<small>Design Playground and Shortcuts</small>
</center>

### 设计游乐场

几周前，我们全力以赴，将整个应用程序改为使用新的[材料设计组件主题](https://material.io/design/material-theming/implementing-your-theme.html)。(Nick Rout 有一篇[很棒的文章](https://medium.com/over-engineering/setting-up-a-material-components-theme-for-android-fbf7774da739)，向你展示了如何为你的应用做这件事的更多细节。)

> ![unknown tweet media content](img/3d8500e9b1ae3e449546e15a3d78fef0.png)![Zarah Dominguez 🦉 profile image](img/3e356e2a494586eef648535b4c5142b5.png)扎拉多明格斯🦉@ zarahjutz![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)我的团队正在砸它。💪06:26AM-03 Jun 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1135432513688465408)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1135432513688465408)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1135432513688465408)16

我们完全预料到一些小部件可能看起来很奇怪，我们可能需要稍微调整一些东西。从 MDC 目录应用程序中获得灵感，我们设计了一个主题展示页面。

这个页面有我们在整个应用程序中使用的所有小部件和自定义组件。

<center>
[![](img/84dd5424478f9089d85537e075a10976.png "source: imgur.com")](https://imgur.com/jfqSBoB)
[![](img/88dc86e11118618aa9692037f99a105b.png "source: imgur.com")](https://imgur.com/zbzyEd0)

<small>Widget showcase</small>
</center>

这对于帮助我们理解如何适应 MDC 非常有用。当我们与我们的设计团队一起调整某些东西时，这个页面也很方便，当我们视觉测试一个新功能时，这个页面也是一个参考页面。

由于我们在应用程序中也有多个主题，我们包括了一个主题切换器，让我们可以看到每个主题中的所有内容。

<center>
[![](img/a085496ad7eb1ba1603406fac4ea5e7c.png "source: imgur.com")](https://imgur.com/pC3qSNY)

<small>Dynamically switch themes</small>
</center>

该切换器通过溢出菜单访问。当用户选择一个主题时，我们将该值保存到一个`SharedPreferences`文件中。