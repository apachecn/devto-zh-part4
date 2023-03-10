# 用 Python 在 Raspberry Pi 上编程:Raspberry Pi 设置

> 原文：<https://dev.to/anuragrana/programming-on-raspberry-pi-with-python-raspberry-pi-setup-58lg>

本文原载于

 **我们将开始一系列文章“用 Python 进行 Raspberry Pi 编程”。在这个系列中，我们将每周撰写一篇文章，介绍您可以在 Raspberry Pi 上使用 Python 编程构建的迷你项目。

这是本系列的第一篇文章，我们将带您完成 Raspberry pi 的初始设置。初始设置完成后，我们就可以在 Raspberry Pi 上编写第一个 Python 程序了。

我们用的是[树莓 Pi 3 B+型号](https://amzn.to/2mPXdeL)。

**什么是 Raspberry Pi:**
Raspberry Pi 是一款低成本全功能单板计算机，配有 Broadcom 处理器和可编程 I/O 引脚，您可以在其中连接物理设备和传感器。在 Raspberry Pi 上，你可以从一个 micro SD 卡上运行 Linux、FreeBSD 甚至 Windows 10 这样的操作系统。将它插入显示器、键盘和鼠标，你就拥有了你所选择的操作系统的完整图形用户界面。

**使用 Raspberry Pi 我们能构建什么:**
使用 Raspberry Pi 和 Python 编程能实现什么，只受我们想象力的限制。互联网上充斥着使用 Raspberry Pi 构建不同项目的文章。其中几个是:

*   运动检测和通知
*   家庭自动化
*   构建您的亚马逊回声
*   调频发射机
*   推特机器人
*   遥控汽车
*   还有 n 多个这样的项目

**从哪里开始:**
开始实验我们需要下面的设备。

*   树莓派
*   微型 sd 卡
*   局域网电缆
*   电源适配器
*   用于连接显示器的 HDMI 电缆
*   USB 键盘和鼠标

目前有几个项目是可选的，但在以后的阶段将是必需的。

*   树莓派案例
*   试验板
*   跳线
*   发光二极管
*   电阻

单独购买第一个列表中的项目会很贵。你可以在从 Amazon.com 或[亚马逊](https://amzn.to/2OuJYNc)[购买【CanaKit】提供的入门套件。](https://amzn.to/2AqH750)

[![](img/b03740069f10faae167d4d4ac4afcf68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M8DcYNGp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/75tmf8qdzhg6xp4mk4ws.jpg)

**设置:**

步骤 1:格式化 SD 卡。

如果您从 Amazon.com 购买了 [CanaKit，那么您可以跳过这一步，因为他们会提供预装的 SD 卡。如果没有，则继续。](https://amzn.to/2AqH750)

*   将 SD 卡插入系统并格式化。
*   从[raspberrypi.org](http://raspberrypi.org/)下载 Noobs zip 文件，并将其内容解压到一个文件夹中。
*   将文件夹的内容(不是文件夹本身)复制到 SD 卡。
*   取出 SD 卡。

步骤 2:安装 Raspbian

*   将 SD 卡插入 Raspberry Pi。连接 USB 键盘和鼠标。使用 HDMI 电缆连接显示器。
*   以太网端口中的插入式以太网线。
*   插上电源。
*   电源一插上，树莓 Pi 就启动了。屏幕上将出现要安装的操作系统列表。选择 Raspbian。
*   正在安装操作系统，请稍候。
*   安装后，使用默认用户名`pi`和默认密码`raspberry`登录。

第三步:更新:

*   开放终端。
*   使用命令`sudo apt-get update`更新您的系统软件包列表。
*   使用命令`sudo apt-get upgrade`将所有已安装的软件包升级到最新版本。

暂时就这样了。你可以玩它。让自己熟悉一下。

树莓派是一台小型电脑。如果我们需要一直连接一个大显示器来使用它，那么我们就没有有效地使用它。

在本文的下一部分，我们将看到:

*   如何配置 wifi 设置
*   如何启用 SSH
*   如何在重新启动时连接到 wifi 网络
*   Raspberry pi 如何在每次重启后告诉我们它的 IP 地址，以便我们可以 SSH 到它。
*   和更少的配置。

这些配置将帮助我们从笔记本电脑上控制树莓派。我们可以将 Raspberry Pi 放在任何地方，并从我们的系统将 SSH 放入其中，无需监视器和以太网电缆即可构建项目。

下一篇:[在树莓 Pi 上配置 WIFI 并启用 SSH](https://www.pythoncircle.com/post/650/programming-on-raspberry-pi-with-python-wifi-and-ssh-configuration/?utm_campaign=devto)

[点击此处](https://amzn.to/2Ltvb7z)购买 Raspberry Pi 入门套件。

本文原载于**