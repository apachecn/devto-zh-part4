# 任务 3:在 Linux 中使用命令行用 C#编写“Hello World”

> 原文：<https://dev.to/techbuilder/task-3-hello-world-in-c-using-the-command-line-in-linux-2011>

所以任务就是写第一个程序在控制台屏幕上输出“Hello World”。

老实说，我已经用 C#做了很多次比这复杂得多的事情，但我从来没有在 Linux 上用 CLI 和文本编辑器(在这种情况下是 Sublime Text 3)做过。有两种方法可以做到这一点，我会在这篇文章中讨论这两种方法。

# 第一种方法使用 Mono 编译器，在执行以下步骤之前应先安装 Mono 编译器:

1 .打开崇高的文本，创造新的。包含以下代码行的 cs 文档:

使用系统；

类 hello world {
static void Main(){
控制台。WriteLine(“人肉肉向你问好！”);
}
}

2 .保存文件。

3 .打开终端，将当前工作目录更改到。cs 文件已保存。

4 .然后使用下面的命令生成。cs 文件。

MCS-out:hello world . exe hello world . cs

mcs:引用 4.0 概要库(API 如。NET 4.0)并支持 C# 4.0

5 .使用下面的命令运行代码。

莫诺 HelloWorld.exe

# 第二种方法采用了。Net Core CLI 工具，您不必为此安装 Mono 等第三方编译器:

1 .打开命令提示符并创建一个新文件夹。导航创建的文件夹，然后通过以下命令创建一个新的控制台项目:

$ dotnet 新控制台

此命令将在您的文件夹中创建两个文件。首先是。csproj 项目文件，其中包含构建控制台应用程序所需的依赖项，第二个文件是 program.cs 文件，您可以在其中编写所需的代码，并作为应用程序的入口点。

2 .在文本编辑器中将 program.cs 中的代码适当地更改为所需的代码。

3 .将目录切换到通过 do 创建的项目

$ cd

4 .然后使用命令运行代码

$ dotnet 运行

点击下面的链接查看本系列的上一篇文章:
[https://dev . to/mechatrona/task-2-install-the-sublime-text-3-netcore-2-x-git-in-Ubuntu-27i 9](https://dev.to/mechatrona/task-2-install-the-sublime-text-3-netcore-2-x-git-in-ubuntu-27i9)