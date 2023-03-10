# 如何(以及为什么)改变你的 Android Cordova 应用程序的 minSdkVersion

> 原文：<https://dev.to/johnnymakestuff/how-and-why-to-change-your-android-cordova-app-s-minsdkversion-f1p>

## TL；速度三角形定位法(dead reckoning)

例如，将 minSdkVersion 设置为 24，在`config.xml`的 Android 平台部分添加:

```
<platform name="android">
    <preference name="android-minSdkVersion" value="24" />
    ...
</platform> 
```

确保**移除并重新添加平台**，否则将无法工作！

```
cordova platform rm android
cordova platform add android 
```

关于注意事项和测试其工作的方法，请继续阅读。

## 什么是 minSdkVersion

`minSdkVersion`是`AndroidManifest.xml`文件中`<uses-sdk>`标签的属性。来自[文件](https://developer.android.com/guide/topics/manifest/uses-sdk-element):

> Google Play 使用您的应用清单中声明的属性来过滤不符合其平台版本要求的设备中的应用。

因此，如果你想让你的应用程序在 Play Store 中只供装有 Android Nougat (7.0)和更高版本的设备下载，你应该设置`<uses-sdk android:minSdkVersion="24">`。

## 为什么要更改 minSdkVersion

如果你正在构建一个生产 cordova 应用程序，为了让你的应用程序流畅美观，大多数情况下你会使用最新的 JS / HTML / CSS。问题是，许多安卓设备仍在使用旧版本的操作系统。旧操作系统版本==旧 WebView，不支持许多新的网络功能。

我的应用程序的一些活跃用户仍在使用 Android 4.0 - ICS。ICS 最新发布是 7 年前的 2012 年 6 月！谷歌不再支持它，而且几乎可以保证你的新 cordova 应用程序性能非常差(如果它能工作的话)。性能差/崩溃==差评。你不想那样。

[![bad-review2](img/2719fb5a7a9601c45e264b9b8bbecbd0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R1xiHhZg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.learningsomethingnew.com/cordova-babel/bad-review2.png)

科尔多瓦应用中默认的 minSdkVersion 目前是 API level 19 - Android 4.4 KitKat。你的 cordova 应用可能在 Android 4.4 上工作，但在 T2 可能不会工作得很好。所以你有两个选择:

1.  构建您的应用程序，以便它可以在 Android 4.4 上工作，Android 4.4 具有 Chrome 版本 30 的 WebView(古代！)
    这将包括大量的传输、调试和彻底放弃 css `grid`之类的东西，Chrome 30 不支持 css，将来也不会支持。你可以使用 [modernizr](https://modernizr.com) 来检测功能是否可用，并根据功能提供不同版本的应用程序，但这需要相当多的额外工作。

2.  决定更高的最低版本，并阻止所有使用较低版本的用户下载您的应用程序。在我的新应用中，我决定使用 API 等级 24 的 Android 7 牛轧糖。

> 我在[这篇文章](https://www.learningsomethingnew.com/should-you-transpile-your-cordova-app)中已经写了更多关于目前 Android / iOS 版本的市场份额以及相应的 WebView 浏览器版本。

# 如何设置 minSdkVersion

你不希望在 Android 项目中直接做任何事情，因为每次你构建 cordova 应用程序时它都会被覆盖！所以在`AndroidManifest.xml`中设置`android:minSdkVersion`是不可能的！

与 React react-native 相比，使用 cordova 的一个主要优势是您永远不必手动编辑本机项目。您只有 cordova 项目，本地版本是根据配置生成的。这使得您的项目能够更新到新的 cordova 版本，并且不需要对您安装的每个新插件/更新进行本机调整。

根据 cordova [文件](https://cordova.apache.org/docs/en/latest/config_ref/)，可以设置这个值

```
<platform name="android">
    <preference name="android-minSdkVersion" value="24" />
    ...
</platform> 
```

唉，如果你把这个添加到你的配置中，生成一个 apk 并上传到商店，你会注意到(也许在一些负面评论之后)它没有改变 minSdkLevel！

在这上面浪费了一些时间之后，我在这个 GitHub [问题](https://github.com/apache/cordova-android/issues/686)中发现了一个提示，说你必须移除并重新添加平台才能使它生效。

因此在更改`config.xml`之后，您还必须移除并重新添加平台，以便更改在生成的 Android 项目中生效:

```
cordova platform rm android
cordova platform add android 
```

## 检查工作正常

在上传新的 apk 后，在游戏控制台中可以看到它的工作情况:

[![console](img/fd139d49e96c1e57e96b3f21ebe2e7d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QXbW2KP9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.learningsomethingnew.com/cordova-change-minsdk/store.png)

最好是，您希望有一种方法可以在本地测试您生成的 apk，而不必每次测试都升级版本并上传到商店。

似乎应该有一个更简单的方法来做到这一点，但似乎现在唯一的选择是反编译 apk 文件。为此，我使用了 Connor Tumbleson 的开源 apktool。你可以在这里获得最新版本[。](https://bitbucket.org/iBotPeaches/apktool/downloads/)

下载后，使用`cordova build android`构建您的 cordova 应用程序，并在 shell 中运行:

```
java -jar <path-to-apktool.jar> d <path-to-cordova-project>/android/app/build/outputs/apk/debug/app-debug.apk 
```

它将在您运行命令的文件夹中生成一个名为`app-debug` *的文件夹。进入那个文件夹，打开`apktool.yml`。您应该会看到类似于* 

```
sdkInfo:
  minSdkVersion: '24'
  targetSdkVersion: '28' 
```

这样，您可以在上传到商店之前确保您的更改生效。