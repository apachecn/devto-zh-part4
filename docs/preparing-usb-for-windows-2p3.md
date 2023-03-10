# 为 Windows 准备 USB

> 原文：<https://dev.to/th3n00bc0d3r/preparing-usb-for-windows-2p3>

*注意:请将您的重要数据备份到 USB 或外部硬盘上，如果是台式机，我强烈建议您断开任何可能包含重要数据的备用或其他硬盘。*

首先，我们需要一个 8GB 的 u 盘。笔驱动器将被用作虚拟 DVD ROM，我们将在其中刻录操作系统的映像，在这种情况下是 Windows 10。现在我们将使用微软网站上正式提供的 windows 10。

现在转到以下网址

[https://www.microsoft.com/en-us/software-download/windows10](https://www.microsoft.com/en-us/software-download/windows10)

如果你在 Chrome 或 Firefox 中打开它，我们将无法获得微软不共享的 ISO 链接。为了能够获得链接，为每个用户激活 24 小时，我们需要在 **_Internet Explorer 中打开它。_** 于是在 ie 浏览器中打开链接。

[![](img/877aa9c35c525e35bc9b7c769057b50d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9rSiUX20--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ipatz1oz6lavtu63x1ri.PNG)

进入 internet explorer 后，点击屏幕右上角的图标，进入设置菜单，从设置菜单中选择 F12 开发工具。

[![](img/492ed17915d288790637bc2ec2f13214.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r4Q5Q6g5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0bmt8esmu4z01bma8hd7.PNG)

一旦你选择了 F12 开发者工具，一个窗口将在底部层叠，默认的 DOM Explorer 打开。在窗口上，最后一个选项卡是仿真。点击仿真。

[![](img/5bc45057096e4ab4ef37d2bf6f500843.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MaS5HJ7o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o3ndv4lw5zlis5jn8nto.PNG)

在“Emulation”选项卡中，您会发现一个名为“User agent string”的下拉列表，单击该下拉列表。

[![](img/a12bc18da955bc702e9fe616ea28e0c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---mgtns4I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sgetyfhvmnmayghrpmtf.PNG)

从下拉列表中，向下滚动找到苹果 Safar (IPAD)。选择它后，页面将会刷新。

[![](img/c22a1dbf9ce50a25daa04bede2a154e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1CixchPS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j4szdszrey3cv7ezflhc.PNG)

随着页面的刷新，现在浏览器正在让微软网站相信你在用苹果 IPAD，在 Safari 浏览器上浏览。这将使微软网站要求您选择版本。单击下拉菜单后，您会发现 Windows 10 2019 年 5 月更新，在它下面是 Windows 10，通过单击选择它。接下来它会问你语言，我选择了英语，你可以根据你的喜好选择。

[![](img/4918526409aeb5a102177afeffbd1b2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EvbKJZpi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gl32xjehrh9i00vv5q92.PNG)

一旦选中，它将再次开始加载，你会看到一个有两个按钮的对话框。选择 64 位下载。现在您将下载一个 ISO 文件。

[![](img/1962f05550997d908560a976a41c8301.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j92DL_ze--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wo13hdfvckeiyo7e52dy.PNG)

文件名:win 10 _ 1903 _ V1 _ 英语 _x64.iso

太好了，给自己一个大拇指，你刚刚欺骗了微软，下载了一个 windows 的 ISO 镜像，没有任何版权问题。

现在我们需要另一个有趣的软件叫做 rufus，可以从下面的链接下载，我很高兴这是一个直接的链接。

[https://github . com/pbatard/rufus/releases/download/v 3.6/rufus-3.6 . exe](https://github.com/pbatard/rufus/releases/download/v3.6/rufus-3.6.exe)

现在，您应该特别需要两个文件。

*   win 10 _ 1903 _ V1 _ 英语 _x64.iso
*   Rofus-3.6.exe

[![](img/8d91c92542b7f9a42a149cfa15e5bd91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kFB6YcH2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0vp3o4mrbgfsm4k0bp1p.PNG)

双击 Rofus-3.6.exe，我们就可以运行这个程序了。一旦程序出现，您应该看到如下所示的屏幕。此时，没有 USB 连接，如顶部的设备顶部所示，它将在底部显示 0 设备。

[![](img/5ed589876d729b2defd20c1643e99363.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TuvBKbSS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/40yh5xnlwxp5dad6j6ye.PNG)

现在是时候插入你的 USB 了。请记住，所有的数据将从 USB 中删除，所以不要保留任何重要的文件。一旦您插入 USB，它应该会自动检测并显示在设备标签。我插了一个 16GB 的 USB。现在点击选择。

[![](img/3f4bf3b1c05de13cff51e1a575f9d630.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yb9CGnTN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b56brklor67gbs3tkv40.PNG)

从浏览器中，浏览到 win 10 _ 1903 _ V1 _ 英语 _x64.iso 文件，然后单击打开。

[![](img/daa053d222de8a3ba654405c7358bb1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g7z7S9qW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qebrj565lxzkff7bvsv9.PNG)

现在，该屏幕将识别默认配置。

重要的事情

操作系统已经转向一种叫做 UEFI boot 的东西，这就像现在你买了一台新的笔记本电脑，感觉好像没有开始屏幕，电脑直接启动到 windows。这个叫 UEFI，旧的叫 LEGACY。

现在的 UEFI 比以前的要快得多，它与硬件的驱动程序有更好的沟通。与 LEGACY 或 UEFI 相比，同一驱动程序上的相同 3D 卡确实在 FPS 方面提供了稍高的增益。

[![](img/8c88836153336aa08d11a3951edca7cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sEZDCZbG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xb9quvh38w1eaihukxqg.PNG)

现在我们在哪里设置这个选项？在 BIOS 中。

现在，如果你的 BIOS 中没有 UEFI 选项，只需点击它并选择 MBR。如屏幕所示。

一旦这是解决，点击开始，并等待它完成。

[![](img/ffe7ec96e29167af2f1d09b67d683809.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ijIEF0Oi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ei6nxi0g49tg4ehbugkh.PNG)

你有一个 WINDOWS 安装 USB 准备好了，让我们准备好隆隆！！！！

对于任何问题和疑问，请使用评论部分。

[下一步:配置 BIOS](https://dev.to/th3n00bc0d3r/configuring-bios-5ep5)

[Noob 指数](https://dev.to/th3n00bc0d3r/noob-guides-index-4mne)