# 如何使用夏洛克搜索社交媒体账户？

> 原文：<https://dev.to/powerexploit/how-do-i-hunt-down-social-media-accounts-using-sherlock-511i>

"**社交媒体是黑客发现任何特定个人或目标信息的最容易的平台之一。——安基特·多巴尔**”

今天！我早上醒得很晚，后来在 9:30 我吃了早餐。接着发生了一件事，我的一个朋友给我发了一个链接，是关于一个工具**夏洛克**的，它可以用用户名追踪社交媒体账户。

就像我平时做的那样！！我拿着一杯咖啡，在一片欢呼声中坐在我的电脑前，启动我的操作系统。
然后我通过[的 github 链接](https://github.com/sherlock-project/sherlock)的**的** &开始了解那个工具。

# **夏洛克是什么？**

夏洛克是一个基于 python 的工具，它可以揭示同一个人在多个社交媒体平台上用他们的**屏幕名**或**用户名**创建的多个用户账户。
夏洛克文字是用 python 写的。你可以从它的 [github](https://github.com/sherlock-project/sherlock) 链接查看整个脚本。
[![](img/39035472736148fe4438e3e2b7f8c533.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mD1YVyib--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i5s59ny60iirfedqyno4.JPG)

## **我如何追查夏洛克的社交媒体账号？**

**步骤 1 - >在终端窗口中安装夏洛克**
我从 [github](https://github.com/ankitdobhal/sherlock) 中复制了夏洛克的链接，并运行以下命令。

[![](img/0b76a10b2d4c48fa64b14eda008edaad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pRbNCDSI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ox2dolbvwaz1ko6cb2i.JPG) 
**注意:Python3 和 python3-pip 都得装。**

为了获得关于夏洛克的帮助，我尝试了在夏洛克文件夹中使用 python 夏洛克-h 命令。

[![](img/41842addd410ee3ba3a60390026c6deb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--98z-JD6m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/947hil9pokhs7zqkzf6k.JPG)

**步骤 2 - >识别屏幕名称**
刚刚看完帮助我就准备开始脚本。但是等等，如何找到我的目标的网名呢？然后我通过谷歌搜索和“推特”找到了我目标的网名

我的目标是罗伯特·巴普蒂斯特爵士，又名埃利奥特·奥尔德森。他是法国安全研究员&，我非常钦佩他在安全方面的工作。

[![](img/efc8a75b7fd0d61e1b74cce5df6add52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tpuB7o1A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mii62u4ur7lqkmjgp3bc.JPG)

**步骤 3 - >扫描账户**
然后最后我在我的终端屏幕上用以下命令运行 sherlock.py 脚本**python target _ name-r-print-found**找出我的目标的所有社交媒体账户。

[![](img/6d5a263387c0a803914d543fdbf45db8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6Tnk4VIk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ux8c4jwmmzjsza9ljsox.JPG) 
**注意:上述命令中的:'-r '参数将按哪些网站最受欢迎来组织找到的
帐户列表，& ' - print-found '将
在终端屏幕中显示所有帐户。**

正如你在上面的例子中看到的，我是如何使用 sherlock.py 脚本在互联网上搜索帐户的。我也试着在这个工具中贡献一些文件，你可以通过这个链接找到[夏洛克](https://github.com/ankitdobhal/sherlock)工具。【https://github.com/sherlock-project】->-