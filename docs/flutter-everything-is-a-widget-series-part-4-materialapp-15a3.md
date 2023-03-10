# Flutter: Everything is a Widget 系列-第 4 部分:MaterialApp

> 原文：<https://dev.to/topeomot/flutter-everything-is-a-widget-series-part-4-materialapp-15a3>

在使用 Flutter 时，你很快会想到的一个主题是，一切都是小部件。本系列旨在帮助初学者理解这个简单而强大的概念，并向他们介绍 Flutter 中的基本小部件。

为了在这个旅程中帮助我们，我仅使用 Flutter 自带的内置小部件构建了一个 Twitter 移动克隆应用。你可以在 https://github.com/topeomot2/twitter-ui-app 找到代码

* * *

每个 Flutter 应用程序都是从调用 main()开始的，main()调用 runApp 命令。runApp 命令只是设置应用程序的根小部件。我们简单地用根小部件输入命令，然后从那里开始构建我们的小部件树。

```
void main() => runApp(MyApp());
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Twitter UI',
      theme: new ThemeData(
          primaryColor: DarkModeBg,
      ),
      home: TwitterHomePage(),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### MaterialApp

根据颤振 SDK 的评论

> MaterialApp 是使用材质设计的应用程序。

材料设计是谷歌在 2014 年推出的一种设计语言。通过设计语言，我们指的是有助于给出独特但一致的外观和感觉的指导方针。材料设计就是这样一种设计语言，在手机和网络应用中变得流行。

每个主要框架都有一个可供使用的材料设计元素端口。

Material-UI:一个流行的 React UI 框架

[Vue 材料设计组件框架- Vuetify.js](https://vuetifyjs.com/en/)

对于 Android 应用程序，材料设计已经成为事实上的标准，并且受到谷歌的鼓励。在材料设计应用程序中，有几组预期的组件(根据材料设计指南设计)。

[安卓材料设计|安卓开发者](https://developer.android.com/guide/topics/ui/look-and-feel)

### 颤振与材料设计

Flutter 团队也希望开发人员可以使用这些组件。

> 他们想出了一个方便的小部件，它包装了材料设计应用程序通常需要的几个小部件。

这个便利的小部件叫做 MaterialApp。因此，如果您想构建一个使用现成材料设计准则的应用程序，您只需将您的屏幕包装在 MateriaApp 小部件中，就可以获得一些非常棒的预构建小部件。

[颤振材质组件 widgets](https://flutter.dev/docs/development/ui/widgets/material)

### CupertinoApp

材质设计语言并不是颤振中唯一支持的语言。材料设计语言在 Android 应用中使用很多，对于 IOS 应用，我们有 Cupertino 设计语言。

CupertinoApp 是在 flutter 中实现这一点的便利小部件。

[Flutter Cupertino (iOS 风格)widgets](https://flutter.dev/docs/development/ui/widgets/cupertino)

* * *

我使用这些设计语言中的一种来给我的应用程序一个一致的外观和感觉，这样它们看起来就像是用 Android 和 IOS 中的原生 SDK 构建的应用程序。他们节省了很多时间。