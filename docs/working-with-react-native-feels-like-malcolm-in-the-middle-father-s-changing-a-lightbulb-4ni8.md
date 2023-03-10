# 和 React Native 一起工作感觉就像马尔科姆在中间父亲在换灯泡

> 原文：<https://dev.to/cutiko/working-with-react-native-feels-like-malcolm-in-the-middle-father-s-changing-a-lightbulb-4ni8>

我已经做了 5 年的 Android 开发者，最近，我开始接触 React Native。这是我对它的感觉

[![malcom ligthbulb](img/ab465eda3d055ac6f5e1cac9c4bcdd2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bGJ16P96--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://firebasestorage.googleapis.com/v0/b/learningfirebase-77a34.appspot.com/o/dev_dot_to%252Fhall_ligth_bulb.gif%3Falt%3Dmedia%26token%3D50528c75-60b0-4609-9bbf-50ecfcbe906a)

老实说，乍一看，glance 对我来说就像是一个构建应用程序的过于简化的层。我一直在学习不要以封面来判断一本书。所以最终我发现可以用原生代码。顺便说一下，这是我目前的努力。

在我看来，一个长期的项目可以从共享代码中获益，任何由于操作系统带来的麻烦都应该是本地的。你不必有两个项目来创建结果列表，但如果你想在应用程序关闭时获得用户位置，你可以从本机实现中受益(我在这里考虑的是 Android)。

现在有趣的是:

*   好了，我要安装我的依赖项...一切都失败了，因为我的节点是用`sudo`安装的
*   好了，现在我卸载了 node，重新安装没有`sudo`(其实是 nvm)...我现在必须重新安装所有的东西
*   好了，在 Android Studio 中，我有了这个插件，可以使用快捷键重启应用程序...而网络风暴却没有
*   好了，现在我想出了几个[命令](https://gist.github.com/cutiko/dd9d0b3facb34a61dc0b2b9cf4c91cc0)来解决这个问题，现在...如何更改包名？
*   好了，现在我把 Android 和 IOS 的包名改成了标准的 *com.subdomain.appname* 现在我要工作了...等待类型脚本似乎有一个问题
*   好了，现在我的项目有了打字稿，我可以工作了！
*   好的，最后一个依赖项告诉我`npm audit fix`，当然...等待更新反应-原生版本？
*   好了，现在我必须用 AndroidX 解决这个新问题(我看到它来自[英里外的](https://twitter.com/CutikoLive/status/1146453755589734403)...当然，让我们添加正确的依赖项，然后添加 jetifier 和...
*   嗬！所以现在我发现了另一个错误，我无法运行我的应用程序...
*   好了，现在我要回滚所有内容，现在我确信我可以工作了....(我会让你知道的，我猜)

关于最近的问题，我没有放弃，必须想办法使用`react-native-cli`这样你就可以看到我的[所以回答](https://stackoverflow.com/a/56892669/4017501)

PS:我知道 live reloading，我只是在改进我的开发环境。是的，我从 react-native cli 中知道了`--typescript template`，但是我忘记了...错误发生在 _(ツ)_/

更新:这是在发布后不久更新的，两天后