# 如何让 React-Native-push-notifications 与 React Native >= 0.60 一起工作

> 原文：<https://dev.to/bdbch/how-to-get-react-native-push-notifications-to-work-with-react-native-0-60-e20>

嘿！所以我几乎一整天都在摆弄[react-native-push-notifications](https://github.com/zo0r/react-native-push-notification)，它的更新周期非常慢。

除了通过 react-native 链接包和在 iOS 中设置本机代码之外，它在 iOS 设备上开箱即可正常工作。

另一方面，在 Android 上，当前可用的 npm 版本的包将与任何使用 **androidx** 的 react 原生项目中断。这是因为 androidx 将旧的模块结构从`android.support`降到了`androidx.`。

有趣的是:修复已经提交给了主服务器。所以你可以通过
从 Github 安装这个库

```
npm install npm install git+https://git@github.com/zo0r/react-native-push-notification --save 
```

现在，您的存储库中应该已经有了修复版本！按照自述文件中描述的 android 设置指南进行操作。

你现在唯一需要做的就是给你的应用程序添加一个 Firebase 和 Google Play 服务版本

将以下值添加到`supportLibVersion`正下方的`ext`值中:

```
googlePlayServicesVersion = "16.1.0"
firebaseVersion = "17.3.4" 
```

我的`ext`配置现在看起来是这样的:

```
ext {
    buildToolsVersion = "28.0.3"
    minSdkVersion = 16
    compileSdkVersion = 28
    targetSdkVersion = 28
    supportLibVersion = "28.0.0"
    googlePlayServicesVersion = "16.1.0"
    firebaseVersion = "17.3.4"
} 
```

现在，您应该能够构建您的 android 项目并在模拟器中运行您的应用程序了！