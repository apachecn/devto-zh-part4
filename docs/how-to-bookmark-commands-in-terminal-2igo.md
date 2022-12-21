# 如何在终端中给命令添加书签

> 原文：<https://dev.to/thamaraiselvam/how-to-bookmark-commands-in-terminal-2igo>

我们总是讨厌在终端中键入或搜索或复制粘贴最常用的命令。

我在终端里找到了一个很棒的缓解导航的工具，叫做**。它可以让你给一个文件夹添加书签，这样你只需输入
就可以轻松导航到该文件夹**

 **`to nameofbookmark`

要在 Ubuntu 中安装 apparix，输入

`sudo apt-get install apparix`

要在 Mac 中安装，请键入

`brew install -v apparix`或[运行这个脚本](https://gist.github.com/glombard/a91a822098aed7bb50a7)

在终端窗口中。

安装后，您需要设置别名`bm`用于书签，设置别名`to`用于转到书签，方法是在您的主文件夹中的`.bashrc file`中添加一些函数(如果您没有这个文件，您可以自己创建)。

您将通过发出命令
找到您需要添加的功能

`apparix –shell-examples`

在终端窗口中。复制 **Bash 风格函数**下面的所有内容，除了 **CSH 风格别名**。将此粘贴到您的`.bashrc file`中。

打开一个新的终端，cd 到你选择的目录，输入

`bm mybookmark`

为文件夹添加书签。之后，你可以进入任何文件夹，输入

`to mybookmark`

转到您的书签。

当然，这个工具也适用于其他 Linux 发行版。

参考:[https://micans.org/apparix/](https://micans.org/apparix/)**