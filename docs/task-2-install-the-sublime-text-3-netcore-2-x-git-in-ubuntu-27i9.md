# 任务 2:安装崇高文本 3，。netcore 2.x，Ubuntu 中的 git

> 原文：<https://dev.to/techbuilder/task-2-install-the-sublime-text-3-netcore-2-x-git-in-ubuntu-27i9>

我按照以下链接安装软件/模块:

1.  sublime text 3:[http://tipsonubuntu . com/2017/05/30/install-sublime-text-3-Ubuntu-16-04-official-way/](http://tipsonubuntu.com/2017/05/30/install-sublime-text-3-ubuntu-16-04-official-way/)
    第一步中的命令需要进行一些修改，基本上是根据文件在我的系统中保存的位置来更改文件的路径。

2.  netcore 2.x:[https://dot net . Microsoft . com/download/Linux-package-manager/Ubuntu 18-10/SDK-current](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-10/sdk-current)
    在安装. netcore 2 . x 的时候我遇到了一个问题。因此，当我按照我分享的链接中的步骤操作时，我在最后一步得到了一个错误(无法定位包 dotnet-sdk-2.2)。
    为了解决这个问题，我按照这个链接上的步骤手动下载并安装了二进制文件:[https://dotnet . Microsoft . com/download/thank-you/dot net-SDK-2 . 2 . 203-Linux-x64-binaries](https://dotnet.microsoft.com/download/thank-you/dotnet-sdk-2.2.203-linux-x64-binaries)
    在第二步中，为了验证安装，我不能使用命令 dot net，而是必须使用 dotnet-sdk.dotnet，因为当。NET Core 是使用默认的 Snap 包安装的。NET 核心命令是，dotnet-sdk.dotnet，而不仅仅是 dotnet。为了安装 snap，我还先配置了 snap

3.  git(只有命令行，没有桌面/UI)[https://www . digital ocean . com/community/tutorials/how-to-install-git-on-Ubuntu-18-04-quick start](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-18-04-quickstart)

点击下面的链接阅读本系列的下一篇文章:
[https://dev . to/mechatrona/task-3-hello-world-in-c-using-the-command-line-in-Linux-2011](https://dev.to/mechatrona/task-3-hello-world-in-c-using-the-command-line-in-linux-2011)

点击下面的链接查看本系列的上一篇文章:
[https://dev . to/mechatrona/task-1-install-Ubuntu-on-windows-laptop-2p 8](https://dev.to/mechatrona/task-1-install-ubuntu-on-windows-laptop-2p8)