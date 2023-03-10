# 具有状态管理的摆动抽屉

> 原文：<https://dev.to/aaronksaunders/flutter-drawer-with-state-management-3g19>

## 入门

*   从 Dart 程序语言的包管理器 [Dart Pub](https://pub.dev/) 安装提供者包，包含可重用库&包
*   提供者包
    *   一个依赖注入系统，为小部件构建了小部件。provider 主要是 InheritedWidget 的语法糖，使常见用例变得简单明了。
    *   [提供商包文档](https://pub.dev/packages/provider)

## 什么样子

| ![](img/e3791411e7a08c354c621fcb22cd215a.png) | ![](img/337c9ae496687c4392008ba417b60d67.png) | ![](img/a60f38b21ec22f0fe70c8caa61f81cc9.png) |
| 主页 | 显示活跃主页的抽屉 | 显示活跃活跃页面的抽屉 |

## 快速概述代码

为抽屉创建一个单独的小部件，并在任何需要的地方使用。

使用[提供者](https://pub.dev/packages/provider)管理抽屉状态，该提供者将跟踪当前选择的菜单项

*   [颤振变化通知文件](https://api.flutter.dev/flutter/foundation/ChangeNotifier-class.html)
*   [Flutter notifyListeners 方法文档](https://api.flutter.dev/flutter/foundation/ChangeNotifier/notifyListeners.html)

该类包含状态信息和修改状态的适当方法。当状态更新发生时，调用 [`notifyListeners`](https://api.flutter.dev/flutter/foundation/ChangeNotifier/notifyListeners.html) 是为了让应用程序知道需要重新绘制适当的窗口小部件

```
class DrawerStateInfo with ChangeNotifier {
  int _currentDrawer = 0;
  int get getCurrentDrawer => _currentDrawer;

  void setCurrentDrawer(int drawer) {
    _currentDrawer = drawer;
    notifyListeners();
  }

  void increment() {
    notifyListeners();
  }
} 
```

向小部件树添加状态管理；我们在这里使用`MultiProvider`是为了演示如何让多个类管理应用程序状态的不同方面。每个都将被添加到`providers`数组属性中。

*   [颤振多供应商文件](https://pub.dev/documentation/provider/latest/provider/MultiProvider-class.html)

```
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MultiProvider(
      child: MaterialApp(
        title: 'Flutter Demo',
        theme: ThemeData(
          primarySwatch: Colors.teal,
        ),
        home: MyHomePage(title: 'Flutter Demo Home Page'),
      ),
      providers: <SingleChildCloneableWidget>[
        ChangeNotifierProvider<DrawerStateInfo>(
            builder: (_) => DrawerStateInfo()),
      ],
    );
  }
} 
```

创建可在应用程序中重用的抽屉小部件。我们跟踪状态以指定哪个菜单项是菜单中的活动项，然后基于此更新菜单项标题的样式

*   [Flutter 抽屉小工具文档](https://api.flutter.dev/flutter/material/Drawer-class.html)

```
class MyDrawer extends StatelessWidget {
  MyDrawer(this.currentPage);

  final String currentPage;

  @override
  Widget build(BuildContext context) {
    var currentDrawer = Provider.of<DrawerStateInfo>(context).getCurrentDrawer;
    return Drawer(
      child: ListView(
        children: <Widget>[
          ListTile(
            title: Text(
              "Home",
              style: currentDrawer == 0
                  ? TextStyle(fontWeight: FontWeight.bold)
                  : TextStyle(fontWeight: FontWeight.normal),
            ),
            trailing: Icon(Icons.arrow_forward),
            onTap: () {
              Navigator.of(context).pop();
              if (this.currentPage == "Home") return;

              Provider.of<DrawerStateInfo>(context).setCurrentDrawer(0);

              Navigator.of(context).pushReplacement(MaterialPageRoute(
                  builder: (BuildContext context) =>
                      MyHomePage(title: "Home")));
            },
          ),
          ListTile(
            title: Text(
              "About",
              style: currentDrawer == 1
                  ? TextStyle(fontWeight: FontWeight.bold)
                  : TextStyle(fontWeight: FontWeight.normal),
            ),
            trailing: Icon(Icons.arrow_forward),
            onTap: () {
              Navigator.of(context).pop();
              if (this.currentPage == "About") return;

              Provider.of<DrawerStateInfo>(context).setCurrentDrawer(1);

              Navigator.of(context).pushReplacement(MaterialPageRoute(
                  builder: (BuildContext context) => MyAboutPage()));
            },
          ),
        ],
      ),
    );
  }
} 
```

在你的一页中使用抽屉；当使用抽屉时，我们在创建实例时指定抽屉的名称。这可能是一个常量，甚至可能是一个包含附加信息的对象，但这对于本例是有效的。

```
class MyAboutPage extends StatefulWidget {
  @override
  _MyAboutPageState createState() => _MyAboutPageState();
}

class _MyAboutPageState extends State<MyAboutPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('About Page'),
      ),
      drawer: MyDrawer("About"),
    );
  }
} 
```

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ aaronksaunders ](https://github.com/aaronksaunders) / [扑动 _ 抽屉 _ 细节](https://github.com/aaronksaunders/flutter_drawer_detail)

### 展示抽屉和带有状态管理的主从页面结构的示例 flutter 应用程序

<article class="markdown-body entry-content container-lg" itemprop="text">

# 具有状态管理的摆动抽屉

一个新的 Flutter 项目演示了一种模式，用于管理应用程序中抽屉之间的状态，并使用一个小部件跨多个应用程序页面表示抽屉

## 入门指南

*   从[https://pub.dev/](https://pub.dev/)安装供应商包
*   提供者包
    *   一个依赖注入系统，为小部件构建了小部件。provider 主要是 InheritedWidget 的语法糖，使常见用例变得简单明了。
    *   [提供商包文档](https://pub.dev/packages/provider)

## 它看起来像什么

| [![](img/e78696b8b80727a219782aa3206e3e21.png)](https://raw.githubusercontent.com/aaronksaunders/flutter_drawer_detail/master/screenshots/home-page-list.png) | [![](img/d8b902958b83581b5e0afc65b3862fbf.png)](https://raw.githubusercontent.com/aaronksaunders/flutter_drawer_detail/master/screenshots/active-drawer-home.png) | [![](img/d21a1fba82d06376e9faec2f2c31c4b0.png)](https://raw.githubusercontent.com/aaronksaunders/flutter_drawer_detail/master/screenshots/about-page.png) | [![](img/7084c55f92ab28c4211c596b4c2434b0.png)](https://raw.githubusercontent.com/aaronksaunders/flutter_drawer_detail/master/screenshots/about-active.png) |
| 主页 | 显示活跃页面的抽屉 | 关于详细信息页面 | 显示活跃页面的抽屉 |

## 代码快速概述

为抽屉创建一个单独的小部件，并在任何需要的地方使用。

使用[提供者](https://pub.dev/packages/provider)管理抽屉状态，该提供者将跟踪当前选择的菜单项

*   [颤振变化通知文件](https://api.flutter.dev/flutter/foundation/ChangeNotifier-class.html)
*   [Flutter notifyListeners 方法文档](https://api.flutter.dev/flutter/foundation/ChangeNotifier/notifyListeners.html)

该类包含状态信息和修改状态的适当方法。当状态更新发生时，对 [`notifyListeners`](https://api.flutter.dev/flutter/foundation/ChangeNotifier/notifyListeners.html) 的调用是调用…

</article>

[View on GitHub](https://github.com/aaronksaunders/flutter_drawer_detail)

如果这是你的第一个 Flutter 项目，一些资源可以帮助你开始:

*   [实验室:编写你的第一个颤振应用](https://flutter.io/docs/get-started/codelab)
*   [食谱:有用的颤振样本](https://flutter.io/docs/cookbook)

要获得关于 Flutter 入门的帮助，请查看
[在线文档](https://flutter.io/docs)，其中提供了教程、
示例、移动开发指南和完整的 API 参考。