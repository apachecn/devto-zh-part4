# 使用 Chromebook 进行 Web 开发:第 3 部分

> 原文：<https://dev.to/angelarae63/using-a-chromebook-for-web-development-part-3-4m8i>

我写这篇文章是为了向读者更新我在新买的支持 linux 的 Chromebook 上遇到的 Arm64 架构问题的修复程序。如果你还没有阅读过第一部分第一章和第二章第三章，那么如果你正在考虑开发 Chromebook，这两篇文章很短，值得一读。
我可以很容易地从我的设置中安装 linux，但是开始在寻找应用程序时出现问题。deb 文件)可以在我的 arm64 机器上运行。
不过，通过在我的 Chromebook 上安装 Gnome 软件中心，这个问题已经解决了。
使用在 [Chrome Unboxed](https://chromeunboxed.com/news/installing-gnome-software-center-chrome-os-linux-crostini) 上找到的指令，我在 linux 终端输入了以下命令:

> sudo apt-get 安装 gnome-软件 gnome-packagekit

输入“y”继续，软件包将完美安装....

[![Gnome](img/3fd2e6a8a28132fdcde3dc6a4dd2d7ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1OqwYKJg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tph9f2r675rjzdxw6a71.JPG)

注意，这些封装都是 arm64...呜呼！完成后，运行以下程序:

> sudo apt-get 更新
> sudo apt-get 远程升级

等待这些操作完成，然后重新启动 Chromebook。你可以走了。当你在你的 linux 文件中打开软件应用程序时，你应该会看到以下内容...一个漂亮的图形界面，在这里你可以搜索将在你的系统上运行的应用程序，并轻松下载它们。

[![GnomeCenter](img/91e9782a825f17c8fa0e2fd5ce9e626c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l5ZmbVRh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ujx4rm0ls8m9n5cpf77w.JPG)

所以这个问题得到了令我满意的解决。我希望你们中的一些人会发现这很有帮助！

如果你想试试这款 Chromebook Dev Life，我想提出以下建议:

1.  在 r/crostini 的 linux 支持设备列表上购买 Chromebook，并附带以下警告:购买一台 64 位架构的 chrome book:amd64 和 x86.64 似乎是最佳选择。*或者*买个 [Pixelbook](https://www.amazon.com/Google-Touchscreen-High-Performance-Chromebook-Generation/dp/B07KP9QYRK/ref=sr_1_1_sspa?keywords=Pixelbook&qid=1565479149&s=gateway&sr=8-1-spons&psc=1&smid=A30EPR1Q8JVZG5&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUExUVI5S1VaMVk1SEo4JmVuY3J5cHRlZElkPUEwMDU3MTMwMUlVNlZVM1lCVkpUTCZlbmNyeXB0ZWRBZElkPUEwNzY2NDMwM09LM1hWV0hYNjRFNiZ3aWRnZXROYW1lPXNwX2F0ZiZhY3Rpb249Y2xpY2tSZWRpcmVjdCZkb05vdExvZ0NsaWNrPXRydWU=)
2.  如果你已经有一个 arm64 设备，并希望有更多的应用程序选择，请像我一样做或不做。你可能对单独使用 VS 代码非常满意。
3.  选择英特尔处理器，并获得至少 32gb 的驱动器空间和 4gb 的内存(2gb 会太慢)。
4.  Reddit [r/crostini](https://www.reddit.com/r/Crostini/wiki/index) 和 [Chrome Unboxed](https://chromeunboxed.com) 是在 Chrome OS 上运行 linux 的优秀资源。

我不得不说，我已经用了我的支持 linux 的 Chromebook 两个星期了，我非常喜欢它！我儿子的华硕 ROG 笔记本电脑正在积灰。他今晚会拿回来的。