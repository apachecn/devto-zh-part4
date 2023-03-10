# 从 Unity 快速打开嵌入式 Android SDK 和 logcat 的编辑器扩展

> 原文：<https://dev.to/shiena/unity-android-sdk-logcat-15hl>

# 前言

[Unity2019 在追加模块的 Android SDK 上运行 sdkmanager 很麻烦](https://qiita.com/shiena/items/38ba69ea96f672941dbb)中可以启动使用`sdkmanager`的终端了，所以也支持`adb`，编辑器扩展为 Package Manager 另外，可以从 Unity 上看到 logcat 的[dzonatan/unity 3d-log cat-extension](https://github.com/dzonatan/Unity3D-LogCat-extension)很方便，所以与 Package Manager 对应进行了错误修正，所以一起介绍。

# エディタ拡張のunity-android-sdk-helper

打开 Unity2018.1 以上版本创建的项目的`[Project Folder]/Packages/manifest.json`后，有`dependencies`键，因此如下追加软件包即可安装。

```
{  "dependencies":  {  "com.github.shiena.androidsdkhelper":  "https://github.com/shiena/unity-android-sdk-helper.git#1.0.1"  }  } 
```

[![image.png](img/1996ecc62d5085322491ea94b5ce0005.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XyQMap2E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/tihpjd2uiy2mkar7yg0h.png)

在 Switch Platform 中切换到 Android 后，会添加上述菜单。

*   `Open terminal with android platform tools`将启动通过 PATH 连接到安卓平台工具的终端。 使用`adb`很方便。
*   `Open terminal with android sdk tools`将启动通过 Android SDK Tools 的 PATH 的终端。 使用`sdkmanager`很方便。 另外，在 Windows 的 Unity2019.1 以上版本中，如果是使用嵌入式 Android SDK 的设定，将以管理员权限启动终端。

调味汁放在[shiena/unity-Android-SDK-helper](https://github.com/shiena/unity-android-sdk-helper)上。

# unity 3d-log cat-extension

[dzonatan/Unity 3d-logcat-extension](https://github.com/dzonatan/Unity3D-LogCat-extension)是从 unity 可以看到 Android 的 log cat 的编辑器扩展。
对其进行了以下修改。

*   ~~[【Unity】可以在 Unity 上运行 Android log cat 的“Unity3D LogCat extension”介绍-修复小金井博客](http://baba-s.hatenablog.com/entry/2018/12/27/085000)中介绍的冻结错误~~- -
*   ~~修正为带有与日志等级相应的颜色~~- >PullRequest 已合并
*   ~~因为显示了获取日志时的时间，所以修改为日志自身的时间~~- >PullRequest 已合并
*   ~~支持✨Unity2019.1 以上版本的嵌入式 Android SDK~~- >PullRequests 已合并
*   可选择设备
*   ✨Package 经理対応

[![image.png](img/174cdb4b372b1663bf2d1e6b79bafbb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wpr70vgH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/dlgsx9nvzy1k64pax4rp.png)

与上述 unity-android-sdk-helper 一样，在`manifest.json`中添加以下内容即可安装。

```
{  "dependencies":  {  "com.github.dzonatan.unity3d-logcat-extension":  "https://github.com/shiena/Unity3D-LogCat-extension.git#upm"  }  } 
```

调味汁放在[shiena/unity 3d-log cat-extension](https://github.com/shiena/Unity3D-LogCat-extension/tree/upm)上。

# Android Logcat (Unity2019 以上)

如果是 Unity2019 以上，也可以使用 Unity 官方软件包的 Android Logcat。

*   [【Unity】可以在 Unity 上运行 Android log cat 的软件包现在可以从 Package Manager 安装(现在是预览版) -小金井博客](http://baba-s.hatenablog.com/entry/2018/12/25/090000)

# 参考链接

*   [shiena/unity-Android-SDK-helper](https://github.com/shiena/unity-android-sdk-helper)
*   [shiena/unity 3d-log cat-extension](https://github.com/shiena/Unity3D-LogCat-extension/tree/upm)
*   [dzonatan/unity 3d-log cat-extension](https://github.com/dzonatan/Unity3D-LogCat-extension)
*   [【Unity】可以在 Unity 上运行 Android log cat 的“Unity3D LogCat extension”介绍-小金井博客](http://baba-s.hatenablog.com/entry/2018/12/27/085000)
*   [【Unity】可以在 Unity 上运行 Android log cat 的软件包现在可以从 Package Manager 安装(现在是预览版) -小金井博客](http://baba-s.hatenablog.com/entry/2018/12/25/090000)
*   [Unity2019 在添加模块的 Android SDK 上运行 sdkmanager 很麻烦](https://qiita.com/shiena/items/38ba69ea96f672941dbb)
*   [用 PackageManager 制作自建库的方法](https://qiita.com/frost224/items/9832625f9f1671dd033b)