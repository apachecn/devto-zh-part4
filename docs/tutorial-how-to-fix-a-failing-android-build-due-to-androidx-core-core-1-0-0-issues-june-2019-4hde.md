# 教程:如何修复因[androidx.core:core:1.0.0]问题而失败的 Android 版本(2019 年 6 月)

> 原文：<https://dev.to/julietter/tutorial-how-to-fix-a-failing-android-build-due-to-androidx-core-core-1-0-0-issues-june-2019-4hde>

[![](img/fea5abc91ec117e5f92b754b5e54d483.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_rohEytb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALR5N28VEu0_KgRNsyj_qAw.jpeg) 

<figcaption>照片由[约翰尼·沃利斯](https://www.instagram.com/johnnywharris/)</figcaption>

拍摄

如果你是像我一样的 React 原生开发者，你可能会惊讶地看到你的 Android 构建刚刚工作，失败，失败，再失败。

如果你在 Android Studio 中，问题大概是这样的:

```
Caused by: java.lang.RuntimeException: Manifest merger failed : Attribute application@appComponentFactory value=(android.support.v4.app.CoreComponentFactory) from [com.android.support:support-compat:28.0.0] AndroidManifest.xml:22:18–91
 is also present at [androidx.core:core:1.0.0] AndroidManifest.xml:22:18–86 value=(androidx.core.app.CoreComponentFactory).
 Suggestion: add ‘tools:replace=”android:appComponentFactory”’ to \<application\> element at manifestMerger6369655802117611865.xml:7:5–9:19 to override. 
```

你有没有对自己说“嗯？安卓克斯？我还不支持这一点。”，那就跟着一起吧。以下是如何在不添加 AndroidX 支持的情况下修复该问题。

### 发生了什么？

谷歌昨天(2019 年 6 月 17 日)耍了我们一把，在其`Google Play`和`Firebase`库上发布了[主要版本更新](https://developers.google.com/android/guides/releases)。这意味着，导入的所有依赖项都达到了以下程度:

```
implementation ‘com.google.android.gms:play-services-base:+’ 
```

…(注意`+`)会受到影响。

### 如何修复

1)跳到您的`/android/app/build.gradle`文件，检查您的依赖项。搜索任何一个`play-services`或`firebase` *的实例，看看它是否是最新的版本(`+`)。

```
dependencies {
  implementation "com.android.support:appcompat-v7...
  implementation "com.facebook.react:react-native:+"
  // found one! vv
  implementation "com.google.android.gms:play-services-base:+"
  // found one! vv
  implementation "com.google.android.gms:play-services-maps:+"
  implementation "com.google.firebase:firebase-core:16.0.1"
} 
```

2)前往 [MVNRepository](https://mvnrepository.com/) 并搜索令人头疼的依赖项。在这里，您可以找到您的模块的最新发布版本。将`/android/app/build.gradle`更新到该版本，并添加一个强制标志来处理任何版本冲突:

```
dependencies {
 implementation "com.android.support:appcompat-v7..
 implementation "com.facebook.react:react-native:+"
 implementation ("com.google.android.gms:play-services-base:16.1.0"){ force = true }
 implementation ("com.google.android.gms:play-services-maps:16.1.0"){ force = true }
 implementation "com.google.firebase:firebase-core:16.0.1"
} 
```

3)是时候处理你的`node_modules`了。导航到`/android`并运行以下命令:

```
./gradlew :app:dependencies 
```

这将为您的整个项目生成一个依赖树。

4)抓取树并粘贴到文本编辑器中。搜索`play-services`或`firebase` *的任何实例，并沿着树向上导航，查看模块被使用的位置。在这种情况下，`react-native-push-notifications`有一个`com.google.android.gms:play-services-gcm:+`依赖项。

```
+ — — project :react-native-push-notification
 | + — — com.android.support:appcompat-v7:28.0.0 (\*)
 | + — — com.facebook.react:react-native:+ -\> 0.59.8 (\*)
 | + — — com.google.android.gms:play-services-gcm:+ -\> 17.0.0 (\*)
 | + — — me.leolin:ShortcutBadger:1.1.8 
```

5)给那些包打补丁！如果我时间紧迫，我会使用 [`patch-package`](https://github.com/ds300/patch-package) ，这是一个 genius 库，可以让你对包进行更改，然后为你的项目创建一个补丁。这个补丁将在你或你团队中的某个人运行`npm i`的任何时候通过`postinstall`应用。

在我的例子中，我导航到`react-native-push-notification`的`build.gradle`文件，进行更新，运行:

```
npx patch-package react-native-push-notification 
```

…瞧！我有补丁！

#### 大功告成！

在我的例子中，我需要对`react-native-push-notification`和`react-native-push-notification`都应用补丁。

请注意，这只是一个创可贴，直到你准备好支持 AndroidX 及其所有的荣耀🤖

希望这有所帮助！

*有关更新的依赖关系的完整列表，请导航到[此处](https://developers.google.com/android/guides/releases)。

* * *

👋嗨！我是[朱丽叶](http://www.julietterapala.com)。我在 [Eventric](https://www.eventric.com/) 工作，是一名软件开发员。请在推特上关注我，地址是 [@Juliette](https://twitter.com/JulietteR) 。