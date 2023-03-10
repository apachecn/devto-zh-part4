# 由于 AndroidX 和 Google Play 服务，7 月 17 日之后无法运行 React Native 或 Cordova 项目

> 原文：<https://dev.to/bartmr/not-being-able-to-run-a-react-native-or-cordova-after-17th-of-july-because-of-androidx-and-google-play-services-dop>

在 7 月 17 日 Google Play 服务更新之后，你们中的一些人在构建之前清理了自己的 React 原生 Android 项目，可能会遇到类似于这个
的错误

```
Attribute application@appComponentFactory value=(android.support.v4.app.CoreComponentFactory) from [com.android.support:support-compat:28.0.0] AndroidManifest.xml:22:18-91
        is also present at [androidx.core:core:1.0.0] AndroidManifest.xml:22:18-86 value=(androidx.core.app.CoreComponentFactory).
        Suggestion: add 'tools:replace="android:appComponentFactory"' to <application> element at AndroidManifest.xml:52:5-309:19 to override. 
```

Enter fullscreen mode Exit fullscreen mode

对于普通的 Android 应用程序，解决方案只是[迁移到 AndroidX](https://developer.android.com/jetpack/androidx/migrate) 以及任何不使用 AndroidX 的应用程序，Gradle 通过使用 **Jetifier** 在构建时处理导入语句的转换。但是 Gradle 的 **Jetifier** 只适用于已经打包的 Android 模块。**它不能在原生 React 原生模块**上工作，这些原生模块完整地保留了它们的原生源代码项目，并在启动 React 原生应用时编译。

对于这些情况，解决方案是:

1.  安装[这个包](https://www.npmjs.com/package/jetifier)并遵循它的指示。如果您的项目在那之后仍然没有开始，请遵循下面的额外步骤...
2.  转到您的`android/app/build.gradle`文件，用新的 AndroidX 构建工件路径交换在 [AndroidX 迁移指南](https://developer.android.com/jetpack/androidx/migrate)的 Arctifact 映射部分中的所有依赖项。
3.  转到`android/app/src`中您自己的本机代码，对引用旧构建工件的导入语句做同样的操作
4.  再次运行你的应用。
5.  评论这个帖子:D

祝你好运。顺便说一句，第一篇关于 DEV 的文章。到