# 颤振发展(第 0 天)。

> 原文：<https://dev.to/bugudiramu/flutter-development-day-0-190i>

*   本教程将告诉你关于颤振。如果你不知道，接下来的帖子会向你解释一切。这是一个 30 天的教程，为绝对初学者解释了关于颤振。

*   先决条件:如果你有热情和好奇心。

*   今天，我们将开始设置我们的环境和一些 dart 基础知识。

### 首先大家问什么是颤振？

Flutter 是 Google 的便携式 UI 工具包，用于创建漂亮的应用程序。它直接编译成本地二进制代码，也就是说，它不会像 cordova 和 xamarin 那样包装东西。

Flutter 对于用单一代码库创建 Web 和桌面应用程序也很有用。我们可以使用相同的源代码来创建移动、网络和桌面应用程序。

Flutter 使用 Dart 作为编程语言。

### 颤振结构框图:

[![Flutter Architecture](img/d2d67c7cad4329480282838801243bda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bOPTy4Na--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.donnfelker.com/wp-content/uploads/2019/05/flutter-arch.png)

### 遵循的步骤:

1.  访问 [flutter.dev](https://flutter.dev) 开始在你的机器上安装 sdk。选择您系统的操作系统。

2.  有一个蓝色的大按钮，上面写着 flutter_windows/mac.zip。只需点击按钮，它就会开始下载。

3.  下载完成后解压(不要解压到 c 盘，我建议解压到别的地方，比如。因为它不会弄乱操作系统程序文件。).

4.  更新路径。

*   如果您希望在常规的 Windows 控制台中运行 Flutter 命令，请执行以下步骤将 Flutter 添加到 PATH 环境变量中:
*   从开始搜索栏中，键入“env ”,然后为您的帐户选择编辑环境变量。
*   在 User variables 下，检查是否有一个名为 Path 的条目:如果该条目确实存在，请使用；作为现有值的分隔符，如 Desktop:\ flutter \ bin(F:\ flutter \ bin)。

1.  现在，您可以从机器的任何地方运行颤振命令了。

```
C:\Users\ramu>flutter doctor
Doctor summary (to see all details, run flutter doctor -v):
[√] Flutter (Channel stable, v1.7.8+hotfix.3, on Microsoft Windows [Version 10.0.17763.593], locale en-US)

[√] Android toolchain - develop for Android devices (Android SDK version 28.0.3)
[√] Android Studio (version 3.4)
[√] VS Code (version 1.36.1)
[√] Connected device (1 available)

• No issues found! 
```

### 设置编辑器

*   Install VS Code VS Code 是一个轻量级编辑器，具有 Flutter app 执行和调试支持。
*   从扩展中用 VS 代码安装 Flutter 和 Dart 插件。
*   调用视图>命令调色板`(ctrl + shift + p)`。
*   键入“安装”，然后选择扩展:安装扩展。
*   在扩展搜索字段中键入“flutter ”,在列表中选择 Flutter，然后单击 Install。这也将安装所需的 Dart 插件。
*   通过颤振医生验证您的设置
*   调用视图>命令调色板。
*   键入“医生”，并选择颤振:运行颤振医生。
*   查看输出窗格中的输出是否有任何问题。

*   如果你正在学习这个教程，请留下你的意见👀/说嗨✋/.

*   如果你有任何疑问，请随时问我。