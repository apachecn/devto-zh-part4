# 在 2019 年玩弄 PS3

> 原文：<https://dev.to/igormp/toying-with-a-ps3-in-2019-4cf0>

*这篇文章最初发表在[我的博客](https://igor.mp/blog/2019/07/21/toying-with-ps3.html)上。*

我最近为了它买了一台 PS3。我甚至不打算在上面玩游戏，但是，我想看看它到底有多容易被黑客攻击。由于我得到了一个胖模型(确切地说是一个 CECHG)，它有可能患有臭名昭著的 YLOD，所以我做的第一件事就是打开它，更换热糊。

<figure>

[![PS3's motherboard](img/906954cce2631aa975d7d44d3ead25a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lx0HC91e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xwhz715nqmlbpqj89g84.jpeg)

<figcaption>PS3's motherboard</figcaption>

</figure>

<figure>

[![Close up of RSX and Cell chips](img/a5f215e6ba4db9004ee03aee06790424.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1UtzDLlH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ag7noo2srff4nk5qutg.jpeg)

<figcaption>Close up of RSX and Cell chips</figcaption>

</figure>

待办事项列表上的第二件事是在其中安装一个 CFW。为此，Reddit 上的 [PS3 Homebrew wiki 非常有用，它包含了启动和运行系统所需的所有内容。我甚至安装了几个合法性可疑的游戏，只是为了看看是否一切正常。](https://www.reddit.com/r/ps3homebrew/wiki/index)

我清单上的第三件事是安装 linux，因为为什么不呢？这是相当无痛的，除了一些小细节，我将解释如下，以及 PS3 实际上对于现代标准来说太慢的事实(也许我使用的廉价闪存驱动器也没有帮助)。

首先要注意的是，在 [OtherOS++ wiki 页面](https://www.reddit.com/r/ps3homebrew/wiki/otheros)中，即使我有一个 NAND 控制台，我也必须将文件保存为`dtbImage.ps3.bin.minimal`，而不是从其中删除`.minimal`，否则 Rebug Toolbox 会抱怨文件丢失。此外，如果您的型号只有 2 个 USB 端口(其中一个可能由闪存驱动器使用)，那么拥有一个 USB 集线器是一个好主意。

<figure>

[![Red Ribbon live boot](img/3c1a9801f3d7877838d02f386c6356c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DJ_typ6a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ibmfdeyalb29deec4m9.jpg)

<figcaption>Red Ribbon live boot</figcaption>

</figure>

我注意到的另一件事是，如果出于某种原因，你试图安装带有特定区域设置和非默认语言的 Red Ribbon，例如，以 en_US 为语言的巴西区域设置，安装将会失败。所以请记住这一点，接受它为您选择的语言环境提供的任何语言。

<figure>

[![Neofetch of the system through SSH](img/9423b240371754ecaf026c1873a93791.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1geBfgN_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/68a08db7bu0bjqar0ewh.jpg)

<figcaption>Neofetch of the system through SSH</figcaption>

</figure>

由于 Red Ribbon 基于旧版本的 Debian，并且自 2014 年以来没有发布过任何版本，因此需要一些更新来保持一切有序，正如在其网站上看到的那样。

在应用了推荐的修复和更新后，我尝试用常规的`apt-get upgrade`进行升级。哦，天啊，我后悔了。花了两个多小时才把所有东西都打开。

我想了解的是，似乎没有可用的 I C 模块，这意味着我无法读取温度传感器，因此我无法控制风扇，风扇似乎以最大速度旋转，同时发出令人讨厌的噪音。

我想了解的其他事情是关于[解锁第 8 个 SPE](https://www.psdevwiki.com/ps3/Unlocking_the_8th_SPE) ，[升级到 systemd](https://wiki.debian.org/systemd#Installation) ，设法编写一些利用 SPE 的程序，并安装一个现代发行版，尽管我不确定是否有发行版仍然支持 PowerPC BE，也不确定 PS3 的 Cell 是否能够在 le 中运行。