# 如何在 Windows 上安装 Kotlin/Native

> 原文：<https://dev.to/alexbaban/how-to-install-kotlin-native-on-windows-346g>

## [T1】简介](#intro)

在本指南结束时，您将拥有转换包含代码
的`hello.kt`文件所需的全部内容

```
fun main() {
  println("Hello, World!")
} 
```

到`hello.exe`，一个 CLI(命令行界面)可执行文件，您可以运行它并得到“Hello，World！”打印在终端中。

## 注

如果你想和 Gradle 一起构建，这不是你要找的指南。本指南向您展示了如何通过从终端调用`kotlinc-native`来构建。

## 先决条件

*   在您的系统上安装 Java JDK 或 JRE 版本 8 或更高版本
*   你知道“科特林/本地人”是什么和做什么([https://kotlinlang.org/docs/reference/native-overview.html](https://kotlinlang.org/docs/reference/native-overview.html))
*   您已经阅读了“一个基本的 Kotlin/Native 应用程序”([https://kot linlang . org/docs/tutorials/Native/Basic-kot Lin-Native-app . html](https://kotlinlang.org/docs/tutorials/native/basic-kotlin-native-app.html))，但是出于某种原因，您不想使用 Gradle 和 IDE 进行构建

## 咱们~~走~~科特林/土人

*   从 GitHub 库([https://github.com/JetBrains/kotlin-native/releases](https://github.com/JetBrains/kotlin-native/releases))下载“Kotlin/Native 编译器”
    *   查找“最新版本”,并按照链接“从...下载二进制发行版”
    *   滚动到页面底部，找到“Assets”部分
    *   点击`kotlin-native-windows-...zip`链接
    *   将`.zip`文件保存在您的计算机上
*   将“kot Lin/本机编译器”放在正确的位置
    *   在您的“主目录”(如果您在终端(通常是“C:\Users\YourName”)中运行`echo %HOMEPATH%`，您可以找到它)中创建一个目录`.konan`
    *   从`kotlin-native-windows-...zip`的内容中取出名为`kotlin-native-windows-...`的文件夹，并将其复制到`.konan`目录中
    *   在`kotlin-native-windows-...`目录中，有一个`bin`目录，将这个`bin`目录的路径添加到您的“环境变量……”>【系统变量】>【路径】以便`kotlinc-native`可以从任何位置调用
    *   如果您需要关于“Path”环境变量的帮助，这里有一个很好的指南([https://docs . telerik . com/test studio/features/test-runners/add-Path-environment-variables](https://docs.telerik.com/teststudio/features/test-runners/add-path-environment-variables))
*   试验
    *   打开一个终端，运行`cd /`然后运行`kotlinc-native -version`，如果目前一切顺利，编译器的版本应该显示出来，您(几乎)准备好编译了

## 首次编译

*   创建目录`mkdir C:\apps\kotlin`
*   在`C:\apps\kotlin`中创建一个文件`hello.kt`
*   使用您的文本编辑器，编辑`hello.kt`并将以下代码放入其中:

```
fun main() {
  println("Hello, World!")
} 
```

*   保存`hello.kt`
*   在终端`cd C:\apps\kotlin`
*   运行`kotlinc-native hello.kt -o hello`
    *   因为这是编译器的第一次运行，所以“本机依赖项”将从您的“主目录”下载、提取并保存到那个`.konan`文件夹中(这是一个一次性操作，可能需要几分钟)
*   当编译器完成时，你会在与源文件`hello.kt`相同的文件夹中找到一个`hello.exe`
*   在终端运行`hello.exe`看到“你好，世界！”文本

## 总结

本指南向您展示了如何使用 Kotlin/Native 编译器安装并创建一个“Hello，World”CLI 可执行文件。

现在工具链已经安装在您的系统上，继续创建您的“真正的”可执行文件。

使用编译器的语法是:

```
kotlinc-native <file-name>.kt -o <file-name> 
```

如果您有任何问题，可以通过 Twitter 联系我: [@alexbaban](https://dev.to/alexbaban) 。

快乐的“Kotlin/Native”编码！