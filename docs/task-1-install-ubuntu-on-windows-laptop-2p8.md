# 任务 1:在 Windows 笔记本电脑上安装 Ubuntu

> 原文：<https://dev.to/techbuilder/task-1-install-ubuntu-on-windows-laptop-2p8>

首先，我搜索了在同一台机器上使用多个操作系统的双引导和虚拟机方法的优缺点。我选择使用虚拟机(VMWare Workstation 15 player ),因为我的笔记本电脑有足够的内存来通过虚拟化在主机操作系统上运行来宾操作系统。其次，双引导程序更复杂，我不能同时使用两个操作系统，而使用虚拟机方法是可能的。

其次，我安装了 VMWare Workstation 15 player，这是 VMWare 的一个免费版本，用于学术和非商业用途。

然后我下载了。Ubuntu 19.04 的 iso 文件

打开 VMware player，单击创建虚拟机，然后选择。出现提示时 Ubuntu 的 iso 文件

VMware 有一个名为“简易安装”的功能，如果它识别出要安装的操作系统，就可以快速安装操作系统，因为它识别 Ubuntu，所以不需要做太多事情，即 VMware 会负责配置。

VMware Player 将尝试自行选择最佳设置，因此除了默认设置为 20 GB 的磁盘空间外，我保留了默认设置不变，并将其更改为 22GB。

VMware 默认分配给虚拟机的内存大小是 2 GB，我选择保持不变，因为 2GB 对于 Ubuntu 应该足够了，剩下 6 GB 用于主机操作系统(Windows)

点击下面的链接可以看到本系列的下一篇文章:
[https://dev . to/mechatrona/task-2-install-the-sublime-text-3-netcore-2-x-git-in-Ubuntu-27i 9](https://dev.to/mechatrona/task-2-install-the-sublime-text-3-netcore-2-x-git-in-ubuntu-27i9)

点击下面的链接查看本系列的上一篇文章:
[https://dev . to/mechatrona/my-journey-of-learning-software-development-4imb](https://dev.to/mechatrona/my-journey-of-learning-software-development-4imb)