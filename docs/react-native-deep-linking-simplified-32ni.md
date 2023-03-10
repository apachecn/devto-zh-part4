# 反应本地深层链接简化

> 原文：<https://dev.to/iamshadmirza/react-native-deep-linking-simplified-32ni>

在我们浏览本博客的**如何**部分之前，我们将在 React 原生应用中添加*深度链接*，让我们先浏览一下**什么**和**为什么**以便更好地理解这个概念。我们开始吧。

# 什么是深度链接？

一个*深度链接*仅仅是一个*意图过滤系统*，允许用户通过一个 URL 访问 Android 应用程序中的某个活动。

让我们假设我们在一个电子商务网站(例如:亚马逊)上看到了某种产品(例如一只鞋)，我们想与朋友分享它。因此，深度链接将允许我们共享一个网址，这将使接收者只需点击一下就可以打开确切的鞋类产品页面。

现在这个定义会更清晰:

> 深度链接包括使用统一资源标识符(URI ),链接到移动应用程序中的特定位置，而不是简单地启动应用程序。延迟深度链接允许用户深度链接到内容，即使应用程序尚未安装。

# 为什么深沈？

我们已经在上面的*部分讲述了一个例子，但是在很多用例中，*深度链接*会非常方便。想想营销策略、推荐链接、分享某种产品等。

移动深度链接的最大好处是营销人员和应用程序开发人员能够通过专用链接将用户直接带到应用程序中的特定位置。正如深度链接让网络更有用一样，移动深度链接对移动应用程序也是如此。*

# 如何添加深度链接？

最后，如何创建一个。只需要三个简单的步骤。它们是:

## 涉及的步骤:-

1.  创建项目
2.  编辑 AndroidManifest.xml
3.  构建项目

## 第一步。创建项目

通过运行以下命令创建一个 React 本地项目:

```
react-native init deeplinkdemo 
```

现在我们终于有了一个需要调整的项目，让我们进入第二步。

## 第二步。编辑 AndroidManifest.xml

我们必须在`AndroidManifest.xml`中添加`intent-filter`来指定启动这个特定应用程序的传入链接。

```
<activity
 android:name=".MainActivity"
 android:label="@string/app_name"
 android:configChanges="keyboard|keyboardHidden|orientation|screenSize"
 android:windowSoftInputMode="adjustResize">
 <intent-filter>
 <action android:name="android.intent.action.MAIN" />
 <category android:name="android.intent.category.LAUNCHER" />
 </intent-filter>
 <!--Copy & Paste code from here-->
 <intent-filter android:label="@string/app_name">
 <action android:name="android.intent.action.VIEW" />
 <category android:name="android.intent.category.DEFAULT" />
 <category android:name="android.intent.category.BROWSABLE" />
 <data android:scheme="app"
 android:host="deeplink" />
 </intent-filter>
 <!--To here-->
 </activity> 
```

我希望评论能明确说明该怎么做。让我们更好地理解`intent-filter`。

> `intent filter`是应用程序清单文件中的一个表达式，它指定了组件想要接收的意图类型。

仔细看看`<intent-filter>`内的`<data>`标签。有两个我们必须关心的属性。
将`scheme`视为一种传入链接，将`host`视为 URL。

> 我们的深度链接会是这样的:`app://deeplink`

阅读谷歌文档了解更多信息:
[https://developer . Android . com/training/app-links/deep-linking](https://developer.android.com/training/app-links/deep-linking)

## 第三步。构建项目

转到您的根目录并运行以下命令:

```
react-native run-android 
```

等待项目构建，然后我们将测试我们的深度链接是否正常工作。

# 测试出来

确保你的应用程序在后台运行这个命令:

```
adb shell am start -W -a android.intent.action.VIEW -d app://deeplink com.deeplinkdemo 
```

如果你的包有一个不同的名字，然后编辑命令如下:

```
$ adb shell am start -W -a android.intent.action.VIEW -d <URI> <PACKAGE> 
```

> *注:仔细看看`app://deeplink`。这是我们添加在`intent-filter`中的链接，用于指定我们的应用程序。*

如果您的应用程序成功打开，那么我们的深度链接将按预期工作。耶！

[!['yay'](img/5eb94115fdf3a2e35de8bda1d861e446.png)T4】](https://i.giphy.com/media/hZj44bR9FVI3K/giphy.gif)

# 如何用网址打开

我们之前使用了`app`方案，现在我们将使用`https`方案。
编辑`intent-filter`属性内的`<data>`标签如下:

```
<data android:scheme="https" android:host="www.deeplinkdemo.com" /> 
```

运行该命令:

```
 adb shell am start -W -a android.intent.action.VIEW -d https://www.deeplinkdemo.com com.deeplinkdemo 
```

如果你的应用出现在你面前，欢呼吧。

## 注:

你可以在`intent-filter`中使用多个`<data>`标签，所以像这样完全没问题。

```
<data android:scheme="app" android:host="deeplink" />
<data android:scheme="https" android:host="www.deeplinkdemo.com" /> 
```

> URIs app://deeplink 和"[https://www . deep link demo . com "](https://www.deeplinkdemo.com%E2%80%9D)都立志于这个活动。

你也可以用这两个链接创建一个 HTML 文件，比如这个和*测试* :

```
<html>
<a href="app://deeplink">DeepLink with app scheme</a>
<a href="https://www.deeplinkdemo.com">DeepLink with https scheme</a>
</html> 
```

通过本地主机访问文件或将其放在设备上。点击链接，这将有望启动你的应用程序。

这是系列的第一部分*React Native Deep Linking Simplified*，在第二部分我们将学习**如何在应用启动时处理传入链接并重定向用户**。如果你觉得这篇文章有帮助，一定要分享。

Shad