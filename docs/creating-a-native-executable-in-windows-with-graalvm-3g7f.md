# 使用 GraalVM 在 Windows 中创建本机可执行文件

> 原文：<https://dev.to/skhmt/creating-a-native-executable-in-windows-with-graalvm-3g7f>

[GraalVM](https://www.graalvm.org/) 因为[很多原因](https://www.graalvm.org/docs/why-graal/)相当牛逼。但是最让我兴奋的是从 Java 字节码创建本机可执行文件的能力。这不像 ExcelsiorJET (R.I.P .)，它让你包含一个大的运行时，也不像 [Launch4J](http://launch4j.sourceforge.net/) 和 Oracle 的`javapackager`工具，它们都创建一个指向你的`.jar`和一个打包的 JRE 的虚拟可执行文件。

GraalVM 使*成为真正的*本地可执行文件，而不需要打包的运行时。

不幸的是，GraalVM 的`native-image`，实际上是它对 Windows 的全部支持，都处于早期采用者的状态。有一些不准确的错误消息、一些错误和一些缺失的功能。

但它或多或少是有效的。

## 入门前要知道的事情

对于一个基本的 java 应用程序来说，最小文件大小约为 8.5 MB。这有点多，但也比包括整个 JRE 少得多。

其他一些文章或指南推荐安装用于 Windows 的 Git 和 Python 2.7，但我 99%确信这是为了在运行前构建 GraalVM。

我尝试过的所有 Swing 和 JavaFX 应用都无法在 19.0.0 的 Windows 版`native-image`中构建。

[图像生成选项](https://www.graalvm.org/docs/reference-manual/aot-compilation/#image-generation-options)页面是一个很好的故障诊断资源，你可能需要将你的一些类放到`--initialize-at-run-time=<comma separated list of class/package names>`选项中。

## 在 Windows 中运行 GraalVM 19.0.0 的步骤`native-image`

1.  卸载所有的
2.  获取 GraalVM 19.0.0 的 Windows 版本:[https://github.com/oracle/graal/releases/tag/vm-19.0.0](https://github.com/oracle/graal/releases/tag/vm-19.0.0)
3.  把它提取到容易找到的地方
4.  获取 Microsoft Windows SDK for Windows 7 和。NET Framework 4(ISO):[https://www.microsoft.com/en-us/download/details.aspx?id=8442](https://www.microsoft.com/en-us/download/details.aspx?id=8442)
5.  挂载镜像，直接打开`F:\Setup\SDKSetup.exe`
6.  默认安装应该是完美的
7.  **运行 Windows SDK 7.1 命令提示符**通过进入`Start` > `Microsoft Windows SDK v7.1` > `Windows SDK 7.1 Command Prompt`
8.  根据您是否有`.jar`或`.class`运行以下命令之一:

```
\path\to\graalvm-ce-19.0.0\bin\native-image -jar \path\to\helloworld.jar --no-fallback 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
\path\to\graalvm-ce-19.0.0\bin\native-image \path\to\helloworld.class --no-fallback 
```

Enter fullscreen mode Exit fullscreen mode

`*.exp`、`*.lib`和`*.pdb`文件似乎是构建过程中的工件，它们不需要发布。

不过就是这样，你的`.exe`是造出来的！

## 更新:截至 GraalVM 19.3.0，仍然不支持 Swing 也不支持 JavaFX。

对于支持 Java 11 的 Windows 上的 GraalVM 19.3.0，您将需要`Visual Studio 2017 Community Edition`而不是`Microsoft Windows SDK for Windows 7`，您可以通过免费的 Visual Studio Dev Essentials 帐户[在这里](https://my.visualstudio.com/Downloads?q=visual%20studio%202017&wt.mc_id=o~msft~vscom~older-downloads)获得。在`Start` > `Visual Studio 2017`中，你当然会运行`x64 Native Tools Command Prompt for VS 2017`作为你的命令行，而不是运行`Windows SDK 7.1 Command Prompt`。

Gluon 正在为 19.3.0 提供 JavaFX 支持——它已经为 macOS 和 Linux 提供了 Java FX 支持，Windows 应该很快就会推出。

最后，如果你在 windows 上发布一个用原生映像制作的 exe 文件，你将需要在其中包含`vcruntime140.dll`，大约 84kb。