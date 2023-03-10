# 别忘了给你爸爸打电话👴

> 原文：<https://dev.to/atornblad/don-t-forget-to-call-your-dad-too-3k50>

在这个系列中，我已经描述了为什么我制作名为 *[呼叫妈妈](https://play.google.com/store/apps/details?id=se.atornblad.callmom)* 和 *[呼叫爸爸](https://play.google.com/store/apps/details?id=se.atornblad.calldad)* 的应用程序，以及创建电话提醒应用程序的一些问题，如使用持久存储、显示通知和设置闹钟。第四部分，也是最后一部分，描述了如何构建多个具有相似功能的应用程序，而不会产生不必要的代码重复。如果你只是想要摘要和一些链接，去底部的[摘要部分](#summary)。

# 别忘了也给你爸打电话👴

正如我在本系列的第一篇文章中解释的那样，我创建 [Call Mom](https://play.google.com/store/apps/details?id=se.atornblad.callmom) 应用程序的原因是我的母亲去世了。起初，我用这个应用程序打电话给我的爸爸，所以我很快就开始研究如何创建两个非常相似的应用程序，它们有不同的名称，在文本和颜色资源上有一些小的差异。

我的第一次尝试涉及到一个巨大的重构。两个应用程序共有的所有部分都被分解到自己的模块中，没有任何资源，每个`Activity`类都被制作成一个`abstract`类。然后，在每个应用程序模块中，创建了每个活动类的非抽象实现，将其连接到该应用程序模块中的资源。这种方法很快变得完全不可持续，我开始寻找更好的方法。

## 风味维度拯救

过了一段时间，我了解了[产品口味](https://developer.android.com/studio/build/build-variants#product-flavors)。这种机制主要用于创建不同的应用程序变体。如果你想发布一个免费的应用程序，并提供一个有更多功能的付费版本，**产品口味**可能是你正在寻找的。我已经开始思考我可以在我的应用程序中添加什么来制作*【高级】*版本来销售。这可能不会发生在**叫妈妈**和**叫爸爸**上，但我知道这两个应用程序可以实现为同一应用程序的两种不同风格。

这使得开发更加容易。我只需要一个应用模块，而不是三个杂乱的模块。第一步是配置一个名为`callwho`的**风味维度**，然后为每个 app 配置一个**产品风味**，其中设置了每个 app 包名的最后一部分:

```
// build.gradle file for the app module - not the project

apply plugin: 'com.android.application'

android {
    defaultConfig {
        applicationId "se.atornblad"
        ...
    }

    flavorDimensions "callwho"

    productFlavors {
        calldad {
            dimension "callwho"
            applicationIdSuffix ".calldad"
        }
        callmom {
            dimension "callwho"
            applicationIdSuffix ".callmom"
        }
    }

    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

上面显示的配置将为我的应用程序创建两种不同的**“call who”**风格:

*   产品风味“calldad”，应用 id `se.atornblad.calldad`
*   产品风味“callmom”，应用 id `se.atornblad.callmom`

仅通过这一项配置更改，您现在就可以编译和打包两个完全相同的应用程序。零代码重复的即时代码共享。有趣的是，当你介绍这两种应用程序风格之间的差异时。

首先为每个应用程序创建风味特定的资源。如果你刚刚创建了你的应用程序，你可能有一个类似于
的`strings.xml`文件

```
<!-- This is ./src/main/res/values/strings.xml -->
<resources>
    <string name="app_name">Call Reminder App</string>
    <string name="action_settings">Settings</string>
    <string name="call_button_text">Call Now</string>
    ...
</resources> 
```

Enter fullscreen mode Exit fullscreen mode

一个容易犯的错误是复制这个文件两次，并更改不同应用程序风格之间的不同值。这将导致大量重复。相反，为每个应用程序创建一个覆盖 xml 文件。首先创建两个新的`strings.xml`文件:

*   `./src/calldad/res/values/strings.xml`
*   `./src/callmom/res/values/strings.xml`

```
<!-- This is ./src/calldad/res/values/strings.xml -->
<resources>
    <string name="app_name">Call Dad</string>
    <string name="call_button_text">Call Dad Now</string>
    ...
</resources> 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- This is ./src/callmom/res/values/strings.xml -->
<resources>
    <string name="app_name">Call Mom</string>
    <string name="call_button_text">Call Mom Now</string>
    ...
</resources> 
```

Enter fullscreen mode Exit fullscreen mode

保存这些文件后，当你查看**项目**工具窗口时，你只会看到其中的一个。它看起来会像这样:

```
> values
      colors.xml
      dimens.xml
    > strings (2)
          strings.xml
          strings.xml (calldad)
      styles.xml 
```

Enter fullscreen mode Exit fullscreen mode

这是我开始认为我做错了什么的时候。我一遍又一遍地检查我的`build.gradle`文件，一丝不苟地检查我拥有的每个目录和`strings.xml`文件的拼写。放心吧！这只是 Android Studio 告诉你哪个产品风格是活跃的。

要在产品风格和构建类型之间切换，打开**构建变体**工具窗口。在这里，你可以在应用的不同版本之间切换。从`calldadDebug`切换到`callmomDebug`，你的资源会是这样的:

```
> values
      colors.xml
      dimens.xml
    > strings (2)
          strings.xml
          strings.xml (callmom)
      styles.xml 
```

Enter fullscreen mode Exit fullscreen mode

通过相同的过程来创建特定风味的**布局**、**可绘制图**、**菜单**和其他资源类型。

如果您想要 Java 或 Kotlin 类的特定风格变体，创建一个名为`./src/calldad/java`的文件夹并模仿`./src/main/java`的目录结构。如果风味特定的`java`目录包含一个与非特定的`main`目录完全相同的文件，则使用特定的目录。

## 多维 app 配置🧊

Android Studio 不仅仅局限于一个风味维度。例如，你可以创建一种`callwho`风格来区分打电话给妈妈和打电话给爸爸，创建另一种`market`风格来区分免费广告支持的应用程序和具有额外功能的付费高级应用程序。

向您的应用程序模块的`build.gradle`文件添加另一个维度:

```
// build.gradle file for the app module - not the project

apply plugin: 'com.android.application'

android {
    defaultConfig {
        applicationId "se.atornblad"
        buildConfigField 'boolean', 'ENABLE_ADMOB', 'false'
        ...
    }

    flavorDimensions "callwho", "market"

    productFlavors {
        calldad {
            dimension "callwho"
            applicationIdSuffix ".calldad"
        }
        callmom {
            dimension "callwho"
            applicationIdSuffix ".callmom"
        }
        free {
            dimension "market"
            buildConfigField 'boolean', 'ENABLE_ADMOB', 'true'
        }
        premium {
            dimension "market"
            applicationIdSuffix ".premium"
        }
    }

    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这种梯度配置将产生四种不同的应用程序:

*   呼叫妈妈(免费)，应用 id `se.atornblad.callmom`
*   打电话给爸爸(免费)，应用 id `se.atornblad.calldad`
*   呼叫妈妈(高级)，应用程序 id `se.atornblad.callmom.premium`
*   呼叫爸爸(premium)，应用 id `se.atornblad.calldad.premium`

您可能会注意到的一个细节是，我已经创建了一个名为`ENABLE_ADMOB`的构建配置字段，默认情况下它被设置为`false`，但是对于`free`来说，它被设置为`true`。要用一个`Activity`方法检查这个字段，你可以这样做:

```
@Override
protected void onCreate(Bundle savedInstanceState) {
  if (BuildConfig.ENABLE_ADMOB) {
    MobileAds.initialize(this);
    // ... continue to initialize AdMob ads
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

*   使用产品风格来生成应用程序的多种变体
*   覆盖特定于每个变体的资源或代码
*   将特定资源放在`./src/FLAVOR/res`目录中
*   将特定代码放在`./src/FLAVOR/java`目录中
*   根据需要使用任意多种口味

*封面照片由[詹姆斯·萨顿](https://unsplash.com/photos/qXn5L9BqRbE)在[Unsplash](https://unsplash.com/)T5 拍摄*