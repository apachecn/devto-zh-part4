# 颤振发展(第二天)。

> 原文：<https://dev.to/bugudiramu/flutter-development-day-2-41e6>

*   在上一个教程中，我们讨论了 Dart 编程基础。从这篇文章开始，我们将探索 Flutter，当我们继续做的时候，我们将学习更多关于 Dart 的知识。
*   在第 0 天，我们已经完成了我们漂亮的编辑器( **VS 代码**)的设置，如果你有相当重的机器，你可以使用 **Android Studio** 。
*   让我们在 Flutter 中创建第一个应用程序，方法是在我们的案例 VS 代码中启动代码编辑器，然后按照步骤操作。

*   `ctrl+shift+p`将调用命令调色板。

*   键入“flutter”，并选择 **Flutter: New Project** 。

*   输入项目名称，如 myapp，然后按 Enter 键。

*   为新项目文件夹创建或选择父目录，等待项目创建完成并出现 main.dart 文件。

*   每当我们在模拟器或真实设备中启动我们的应用程序时,`main.dart`文件首先运行。

### 使用真实设备:

#### 如何启用开发者选项:

*   打开“设置”>“关于手机”>“点击内部版本号”7 次。

#### USB 调试:

1.  使用 USB 电缆将手机连接到电脑。
2.  此外，你应该打开开发选项中的 USB 调试。
3.  打开设置>附加设置>开发者选项> USB 调试>打开
4.  如果你正在使用红米/小米手机，那么你应该关闭开发者选项中的 **MIUI 优化**选项(在开发者选项的末尾向下滚动)。

### 使用仿真器:

1.  打开 IDE 并选择 Start a new Flutter project。
2.  选择“颤振应用”作为项目类型。然后单击下一步。
3.  验证 Flutter SDK 路径指定了 SDK 的位置(如果文本字段为空，请选择 Install SDK ...)。
4.  输入项目名称(例如，myapp)。5.然后单击下一步。
5.  单击完成。等待 Android Studio 安装 SDK 并创建项目。
6.  按下运行按钮。

### 创建 AVD (Android 虚拟设备):

*   打开 Android Studio / IntelliJ head 回到右上角的 AVD 管理/选择工具> Android > AVD 管理器并在那里创建一个。

*   如果您想运行应用程序，请按`F5`。

##### *我们走吧我们准备好了！*

#### 编写你的第一个 Flutter App:

```
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Welcome to Flutter',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Flutter'),
        ),
        body: Center(
          child: Text('Welcome to Flutter Development Tutorial'),
        ),
      ),
    );
  }
} 
```

*   按下`F5`或`fn+F5`。它将运行代码，并在您的仿真器/真实设备中启动应用程序。

*   在 Flutter 中，一切都是一个**小部件**。
    Ex。文本是一个**小部件**，中心是一个**小部件**，支架也是一个**小部件**就像在 Android 中一切都是一个**视图**。

*   这里有一些每个人都必须知道的基本部件。

*   首先是资源包，因为它包含了所有要使用的小部件和方法。

##### MaterialApp

*   Material App 是保存所有视图的基本小部件。我们必须用 Material App 包装主应用程序，因为它为我们提供了应用程序的默认视图。

##### 脚手架:

*   Scaffold 通过提供一个 **AppBar** 和一个 **Body** 给了我们默认的应用结构。
*   实现基本的材料设计可视化布局结构。这个类提供了显示抽屉、小吃店和底层的 API。

##### Appbar:

*   一个材料设计应用程序栏。应用程序栏由一个工具栏和潜在的其他小部件组成，如标签栏和灵活的空格键。

##### 容器:

*   一个便利的小部件，它结合了小部件的常见定位。

##### 行:

*   在水平方向布局子部件列表。

##### 列:

*   在垂直方向布局子部件列表。

##### 正文:

*   具有单一样式的一串文本。

##### 图像:

*   显示图像的小部件。

##### 图标:

*   材料设计图标。

##### 凸起按钮:

*   一个材质设计的凸起按钮。凸起的按钮由悬浮在界面上的一块矩形材料组成。

*   在这里，我使用 VS 代码，因为它是轻量级的，我在真实设备上测试我的应用程序，因为模拟器在我的笔记本电脑上非常慢，因为我使用一些旧的东西。希望你能理解。过去我用模拟器，但现在我用我的真实设备。

*   如果你想使用你的真实设备，那么不要忘记在开发者选项中打开 USB 调试功能