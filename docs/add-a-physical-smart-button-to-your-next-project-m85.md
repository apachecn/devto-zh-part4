# 将“物理”智能按钮添加到您的下一个项目中

> 原文：<https://dev.to/wassimchegham/add-a-physical-smart-button-to-your-next-project-m85>

最近，我有一个想法，我想探索:如果我可以建立一个真正的按钮，并使它触发任何使用简单的 URL (webhook)会怎么样？在这篇简短的文章中，我将向你介绍我是如何在一个晚上完成这个项目的:

液体错误:内部
( [见推文](https://twitter.com/manekinekko/status/1148047746416480256))

观看它的实际应用:

[https://www.youtube.com/embed/RmcINVszbMs](https://www.youtube.com/embed/RmcINVszbMs)

我们开始吧...

# 硬件

### 一个物理按钮

首先，我需要一个实际的物理按钮(这似乎很明显，对吗？).幸运的是，几周前我已经在亚马逊上订购了一个[“紧急按钮”](https://amzn.to/2JPhxI3)。我偶然发现了这个很酷的按钮，并决定购买，以防有一天我需要用它来建造一些东西。看来我是对的！

当然，你可以有任何其他按钮，只要确保它有一个闭锁开关(这样更现实！)但是可以随意使用任何其他类型的开关。

### 刨花板(大脑)

我是洋葱欧米茄的忠实粉丝。实际上我有很多(你知道，以防万一)。我在我的每个物联网项目中都使用它们，比如我的“语音控制站立式办公桌”黑客。

[![wassimchegham](img/c977dd27667b65e19458c15e79664ee6.png)](/wassimchegham) [## 嘿谷歌，把我的桌子设置成站立模式！

### 瓦西姆·切舍姆

#javascript #tutorial #node #googleassistant](/wassimchegham/hey-google-set-my-desk-to-standingmode-ai6)

对于这种设置，事情会更容易，因为我们只有 2 根线连接(焊料，来自按钮的开关，进入 Omega2+引脚。

[![](img/af570a6b9d9f159a07ad229b3a6d98c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W-xrGZvf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xqgd99lo491scgxr7j4b.jpg)

这是烧结图:

[![](img/9af69792dfbcae4752029aeb4f82f048.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r8z7LI9L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6mksbiysn51kkff43qg3.png)

> 如果你想了解更多关于洋葱 Omega2+板的知识，这里有一个很棒的[入门教程](https://docs.onion.io/omega2-docs/first-time-setup.html)。

### Wifi 天线

有一点(不是强制的)是洋葱 Omega2+板使用外置 Wifi 天线。这只是为了防止表面安装(SMT)天线导致信号丢失。

[![](img/0309cc9341833827051b061f4646fed6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XQqZk1Uk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jxwxq9i14adrb1f2ejyd.jpg)

对于我的设置，我使用了一个 6dBi 天线，它就放在我的工具箱里。只要确保你的天线与 u.FL 连接器兼容。如果你需要更多关于如何连接这种天线的信息，我强烈推荐你阅读[洋葱指南](https://onion.io/2bt-u-fl-antennas-with-the-omega/)。

[![](img/854cf833b6f71864889f007ba251c29d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gzOk-Ok2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2iyakupcxkw8ayix1ztf.jpg)

[![](img/f5d8fb9a5a4a1f458ec488b0c185a4d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VRKL4Xh8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rm2d1orptytb2t0t0jhj.jpg)

[![](img/b46882e8a6663c34391ab76c1d0e7ee2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--23swfFZG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0h8qtqa6xothcqj8z6sm.jpg)

[![](img/0dc407911679a7350356aeaee376d64c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o8T5Xjz7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8hddu4pxe5islo5ke4zz.jpg)

[![](img/7a70f7e6b4040425ac86d061aa9db2ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a02tx3NY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ajeywwhucdk622p5i3n.jpg)

### 把一切放在一起

最后，我们需要将 micro USB 电缆连接到 Omega2+(为电路板供电),并将所有东西都紧紧地放在按钮盒内:

[![](img/a36c04052f2556d6fe305918e7b0a857.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gFJO-Ips--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/105a8plltix7j6bqnj5q.jpg)

# 软件

说到软件部分，正如我提到的，这是一个简单的设置，我们可以选择任何技术(python、node...)可以与 HTTP 端点通信。对于这个快速 DIY 项目，我只是写了一个快速 Shell 脚本来处理按钮行为。我已经在代码中添加了注释: