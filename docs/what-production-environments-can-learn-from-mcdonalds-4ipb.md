# 什么样的生产环境可以向麦当劳学习

> 原文：<https://dev.to/jpin27/what-production-environments-can-learn-from-mcdonalds-4ipb>

我觉得我的第一篇开发帖子应该是关于我目前的开发工作和入门级工作之间的共同点。

想象一下:你走进一家麦当劳去吃午饭。在常见的菜单项中*(偶尔等待菜单回来，因为广告占据了整个屏幕)*，你会看到一个新的食物，上次你去那里时菜单上没有。

出于兴趣点了菜后，经理会问你对新菜的感觉如何，或者只是偷偷看着你，看看你吃第一口会有什么反应。

这种情况有三种可能的结果:

1.  你**讨厌它**。你只吃了一口，没有吃完，让经理知道你不喜欢这种新的食物。几乎所有其他人也讨厌它，接下来的一周，你再也看不到新的食物了。
2.  你和其他许多人一样喜欢它。这个项目最终成为全国所有麦当劳餐厅的永久菜单项目，甚至可能取代一些没有这个新项目卖得多的东西。
3.  你和**可能有一半的顾客喜欢它。因为不是每个人都讨厌这个新菜单项目，它成为所有麦当劳餐厅的菜单上的“点菜”项目:它不够受欢迎，不能在超值餐清单上有自己的数字。**

这是一个非常普遍的现象，发生在大多数主要的快餐店。当我在麦当劳工作的时候，我们称之为 LTO——一种“限时优惠”——通常只在一个地区推出，看看顾客会喜欢它，然后再决定在全国推广。

这个概念在餐饮业并不是什么新鲜事，在软件开发中也有一席之地。它叫做**金丝雀部署**，包括:

> *“将编程代码变更推给一小部分没有自愿测试任何东西的最终用户”*。

*“向非志愿者部署代码？！多么可疑的生产模式！”*

这个概念听起来可能很可疑(*为什么你要把代码推给非测试人员？但是现实是金丝雀的部署看起来可疑，因为源代码不像汉堡或奶昔这样有形的东西。*

事实上，canary 部署已经在大多数企业软件中实现了。GitLab 有关于[如何启用 canary 部署](https://docs.gitlab.com/ee/user/project/canary_deployments.html)的程序，并将其描述为“流行的持续部署策略”。Klarna，一家为在线企业提供金融服务的公司，在他们的开发博客中建议金丝雀部署[为软件发布增加了巨大的信心。网飞理工大学的博客文章](https://engineering.klarna.com/simple-canary-releases-in-aws-how-and-why-bf051a47fb3f)[“对金丝雀的信心”](https://medium.com/netflix-techblog/deploying-the-netflix-api-79b6176cc3f0)显示，金丝雀部署占据了他们测试管道的很大一部分，就在回归测试和红黑测试之间:

[![Chart showing the Netflix testing pipeline](img/2924d415282da365819f43db08f8f137.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eoWeWBgw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/778akfq7gh1244tvorn2.png)

金丝雀的部署就像三叶草奶昔、栗子果仁糖和所有其他我们没有真正要求添加到菜单上的美味一样常见，但我们仍然会吃。毕竟，我们不断回到麦当劳的原因不就是为了看看我们可以尝试什么样令人兴奋的新汉堡吗？