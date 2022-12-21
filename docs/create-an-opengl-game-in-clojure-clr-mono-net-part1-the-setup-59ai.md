# Clojure CLR 中的 OpenGL 游戏(Mono/。Net) -第 1 部分(设置)

> 原文：<https://dev.to/metacritical/create-an-opengl-game-in-clojure-clr-mono-net-part1-the-setup-59ai>

## 简介

我一直在考虑用`clojure`写 2D/3D OpenGL 游戏。当谈到开发游戏时，你有很多选择。你可以使用 clojurescript + Webgl/Canvas Api 或者你选择的任何 2D/3D javascript 库为 webgl 开发游戏。你也可以使用 JVM 和它的许多 OpenGL 库，比如 LWJGL。

还有第三种选择，如果你想让你的游戏具有高度的可移植性，你可以选择一个 C#绑定的游戏引擎。或者最好使用`Clojure-CLR`在`.Net`框架上开发。这是针对第三个选项的系列教程中的第一个教程。

从 C#开始。Net 是大量独立和小型工作室最受欢迎的游戏开发选择，我认为使用 Clojure CLR 在 Mono/上开发跨平台游戏是一个极好的选择。Net 框架。

本教程适用于在装有 mono/的 macOS 上进行开发。但是它应该可以直接应用在 Ubuntu/Linux 或者 Windows 上。

## 设置。

为了开始在 mac 上开发 Clojure CLR 应用程序，我们需要安装`Mono`，这是微软的一个开源实现。Net 框架在 Linux 和 Mac 上的应用。

在 mac 上安装 mono 最简单的方法是使用 mac 通用软件包，从这里[https://www.mono-project.com/download/stable/](https://www.mono-project.com/download/stable/)

这也将安装`nuget`，它是一个获取`.Net`包的包管理器。

一旦安装了`Mono`，您可以通过在终端上键入以下命令来检查。

`$ mono --version`
T1】

### 安装 Clojure-CLR

现在让我们安装`clojure-clr`，尽管你可以免费试用官方的`Clojure-CLR`本教程使用了由[拉姆齐·纳塞尔](https://twitter.com/ra)和[阿卡迪亚科技公司](http://arcadia.technology/)的合作者维护的`Clojure-CLR`的一个特殊分支，因为我发现用那个`Clojure-CLR`代替官方的二进制文件最成功。

`$ git clone git@github.com:arcadia-unity/clojure-clr.git`
`$ cd clojure-clr/`
`$ git checkout unity`

如果编译成功，这将在`bin/4.0/Release/`中产生一个`Release`目录。现在，您应该将这个目录作为一个条目添加到`~/.bash_profile`中的`CLOJURE_LOAD_PATH`环境变量，这样我们就可以编写构建脚本来查找和使用`clojure-clr`二进制文件。

`export CLOJURE_LOAD_PATH=$HOME/Projects/clojure-clr/bin/4.0/Release/`

将以下别名添加到`~/.bash_profile`
`#Clojure clr repl.`
`alias cljclr="mono $CLOJURE_LOAD_PATH/Clojure.Main.exe"`
`#Clojure clr compiler.`
`alias cljcomp="mono $CLOJURE_LOAD_PATH/Clojure.Compile.exe"`也是有用的

还要记住获取 bash 概要文件:`$ source ~/.bash_profile`。

以上别名分别是 clojure-clr repl 和 compiler。

在终端上调用`$ cljclr`,你会看到一个 clojure repl。

`Clojure 1.10.0-master-SNAPSHOT
user=>`

现在可以在 repl 上运行 clojure 命令了。

您可以按下`Ctrl+c`退出复制器。

我们需要安装的最后一件事是 OpenGL 的`C#`绑定，在 [OpenTK](https://opentk.net) 中的一个这样的包装器。

我们将首先为我们的应用程序创建一个目录结构。

`$ mkdir -p ~/Projects/cljopentk/extern`
`$ mkdir -p ~/Projects/cljopentk/build`
`$ cd ~/Projects/cljopentk/extern`

这会将最新的 OpenTK 绑定下载到 extern 目录中。目录中的 ls 应该显示以下 dll。

`$ ls extern/OpenTK/lib/net20/`
T1】

现在让我们在父目录`cljopentk`中创建一个 core.clj 文件。

`$ cd ..`
T1】

## 构建脚本。

使`build.sh`成为可执行文件，

`$ chmod +x build.sh`

并将下面的代码粘贴到其中。