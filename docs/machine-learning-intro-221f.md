# 机器学习:简介

> 原文：<https://dev.to/brohittv/machine-learning-intro-221f>

[![Alt Text](img/dacad8f59cb57acd23b1001b54ef4dda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dOHy73St--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mc0gblv9jxx1vlcbnepx.png)

在我们周围的世界中，算法无时无刻不在我们的日常生活中围绕着我们，你可能正在阅读这篇博客，因为你在谷歌上搜索了一些东西，通过一些算法搜索了你的搜索词，然后把这篇博客带给你，因为我清楚地了解机器学习的最高水平....(不是)

无论如何，当你点击这个博客时，算法会记录下来...当你访问 facebook 或 youtube 时，有一种算法会根据你喜欢的东西给你带来你在主页上看到的东西，当你在网上买东西时，有一种算法会监视你的购买，并根据其他人可能购买的东西向你提出建议..

[![Alt Text](img/d02750fbd3ae9a96b51cb8794647336b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BnTUMJlC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/944vivd34a50seeb3xy5.png)

在这里，你可以看到它的大学季节，一个算法给我带来了一些东西，当我回到学校的时候，我可能会用到...

你的银行也有算法来监视交易，以确保没有任何欺诈交易..

[![Alt Text](img/7571d4e96f3d4360846af1c70a449fdc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TanU2-aG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s7mnw4cht9megihe6yr4.png)

在股票市场上有一群机器人在交易....

[![Alt Text](img/f10db2249f943b105af2cfe9c747447f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tSINgnmk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hdo16inwi29z6xw7fkbl.png)

你猜对了！更多机器人..

那么这些算法是如何工作的呢？？

在过去的日子里，我们用一些我们可以解释的东西来构建这个算法机器人，比如 If (number === 1) {return number}你会得到输出“1”！

[![Alt Text](img/7f24a1990c295a627b727c29c07a0625.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hueNrmA3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/61onmetgdbq0598wmg5f.png)

但是世界上有许多问题，我们不能只写简单的指令来解决..例如，世界上同时发生数十亿次股票交易，机器人如何确定哪些是合法的，哪些是欺诈的？

youtube 上有数十亿个视频，机器人应该带哪 8 个给你看？在我们的日常生活中，这些机器人不断被问及问题，并给我们答案...现在，这些可能不是完美的答案，但这是它所知道的当时能给我们的最好的答案，而且大多数时候它是相当准确的！

[![Alt Text](img/16c11c22b56c83de867e371f922b8870.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IlXnYe9k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nj8nr4y0dqkwhnww77eh.png)

然而，没有人知道这些机器人是如何工作的..包括“建造”这些机器人的人

这是因为使用这些机器人的公司不仅对信息保密，因为这些机器人对公司来说是非常有价值的员工，而且他们自己也不完全确定...看，这和我指着一只蜜蜂告诉你它是一只蜜蜂时，一个人如何学习是相似的..你能够理解，现在每次你看到一只蜜蜂，希望你能够分辨出它是一只蜜蜂，而不是那些讨厌的黄蜂

但是如果我问你你怎么记得那是一只蜜蜂..你会像 IDK 一样，如果有人问我，我是怎么教你那是一只蜜蜂的，我会说...IDK！虽然可以理解大脑中的单个神经元和神经元簇的代码..整体超出了我们的知识范围

这有点类似于机器学习在基础层面上的工作方式。

[![Alt Text](img/1c87bd943bd3e1805c4c46d4e75ece2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XT_5fPTG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qrbqdzmpx4l2nws12yvj.png)

[![Alt Text](img/69d67b51f842814189c1aee553e6d72f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--17n-k-84--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1m8uutcr1r3zq1tg2o3a.png)

首先，我们必须先建造一个建筑机器人和教师机器人...这些机器人大脑更容易理解，一个真正优秀的程序员只要花时间和精力就能制造出它们。
他们的工作呢？嗯，就像他们的名字暗示的那样..

[![Alt Text](img/07636fd228b35da6d0aa319f09af3f5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T_btNaw7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/83oaedpghjo3e3k364lc.png)

建造者机器人建造机器人，尽管它并不擅长，这是因为建造者机器人朝着一个方向建造这些机器人，但几乎是随机的...

[![Alt Text](img/e802364e8452d30055a8fe34cdc96878.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bzqIB0zV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xys6o5oew1jdiyo9ckk5.png)

这导致了一些漂亮的...供教师机器人使用的特殊学生机器人..是的，老师...然而，教师机器人本身并不知道 3 和蜜蜂的区别，因为如果它知道的话...为什么这个博客甚至是一个东西？

[![Alt Text](img/3c00068f4c9906620754a0860b94f299.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZnI-vkx2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k2cwzz1u1nwago2lbjfu.png)

相反，我们给了老师机器人一堆 3 张照片和一堆蜜蜂的照片，以及它们是什么的答案。

机器人老师不会教书...但是机器人老师绝对可以测试..

[![Alt Text](img/b15e8b8f83877b76b73e98a4af9bb581.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_8QWBFIq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4c1yp9ka6j2lgu5zlkcr.png)

学生机器人在测试中非常非常努力，但是他们做得不好..非常糟糕..好像他们连测试都通不过一样...

[![Alt Text](img/ffe4dae6f267ccd57a4c2580de77f47b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BuL0QuFH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qmk2unhlht55d8nnt6is.png)

这并不是他们的错..我的意思是，毕竟它们是由一个不知道什么是 a 3 和 Bee 的机器人制造的，那么它如何制造一个知道 a 3 和 Bee 是什么的机器人，而只是在机器人大脑中进行随机连接呢..所以可怜的小学生机器人把他们糟糕的考试带回家，不像我六年级的时候，但是他们不知道等待他们的命运...

[![Alt Text](img/94a13a23751deafaf54ff2682eb1d976.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bDQB_YC---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k9gzler7h73vw2xavzmi.png)

做得最好的被放到一边...其他人呢...

[![Alt Text](img/24a4a4e352404c2fa97aa93974f3fc85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GyrJtoqF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mcw9rlmrufpe8blvb8r4.png)

是...

无论如何，建造者机器人会把剩下的机器人复制过来，再做一些随机的改变，然后把这些新机器人再送到学校去..他们中的一些人更聪明

[![Alt Text](img/ba9d8b48462620425cba210abf58dc0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D2R-o3Dz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3gwe77yg03fwv6fuq7aq.png)

这个循环再次发生...再一次...再一次...再一次..

你可能会问我，嘿，Rohit，如果建筑商在随意建造，而老师只教不考，理论上是不可行的，或者至少是没有效率的。你可能是对的，如果我们像普通的学校教室一样只使用 1 到 13 个机器人，而建造者要花很多时间来建造这些新的机器人。

然而，教师机器人不只是教任何常规的旧 13 学生教室，而是教一个无限仓库的学生机器人

[![Alt Text](img/ba9d8b48462620425cba210abf58dc0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D2R-o3Dz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3gwe77yg03fwv6fuq7aq.png)

这个测试不仅仅是 10-15 个问题，而是更像是一百万个问题，所有的问题都是问某个东西是蜜蜂还是 3，每个机器人都被拿回来，并立即用随机或不随机的“改进”进行构建，当它有一百万个随机变化时，其中一个必须最终足够幸运，几乎分辨不出蜜蜂和 3

[![Alt Text](img/af246a3a9d61920dce1ec3605774862f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0pnHEAfj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kpkas0br09efvtpznqf3.png)

随着这个过程不断重复，分数会越来越高，生存所需的分数也会越来越高

[![Alt Text](img/547b953f4b08bbe4cf43723b9ee0c4c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J3DWcVUD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/47qieiprvperds78korr.png)

最终你会得到一个机器人，它可以从一张它从未见过的图片中真正区分出蜜蜂和蜜蜂

[![Alt Text](img/c25bd0bdd279e619327113c63c1c122b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i0znRaY1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lefkh0fjbjfmqwofv8ce.png)

但是学生机器人是如何做到这一点的，建筑机器人和监督这个项目的教师机器人都不知道...在做了这么多随机的改变后，一些有用的一些没有，机器人的代码变得非常复杂...

虽然一行代码和多种功能可以理解，但整体超出了我们的知识范围..

这可能会令人沮丧，因为尽管机器人做了它被教导做得很好的事情...它只擅长那一件事，要教它一件新的事情，你必须重新开始整个过程，在测试中实施新的课程，在我们的场景中，机器人非常擅长图片，但是如果图片是颠倒的或者是视频，会发生什么呢？机器人又被难住了...

[![Alt Text](img/703a7530b7498d750d0437355bafd3fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gq9Mo1H9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5j9697wu6c736ghhk6u8.png)

或者很明显不是蜜蜂的东西..放进蜜蜂文件夹。

[![Alt Text](img/69716da1abeba3fd73284b0c8a7eddd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--im_WSmGu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/efh8mvacty0a8wgqhczc.png)

这就是为什么公司总是收集数据，因为现在人类监督者不得不写更长的测试，更多的问题，并试图包括最好的机器人出错的问题..因此，当你点击图片中的交通信号来检查你是否不是机器人时，它们也在收集信息，看看是什么让我们人类不同于机器人！

这是因为更多的数据意味着更长的测试时间，这意味着更有效的机器人

这是机器学习的基础，接下来我们将讨论如何将这一概念与你的广告结合使用，这些广告正在被跟踪，以使你更长时间地关注某些公司的产品 DUN DUN DUN 如果学生机器人可以像你一样喜欢相同的东西，这里有一些想法...它能找到新的东西让你继续参与吗？

[![Alt Text](img/7bbe971bbab0af979cc8653dd322383a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YDOQ3jus--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jmww7d6xqo805eoijgal.png)

我要感谢:[https://www.youtube.com/watch?v=R9OHn5ZF4Uo](https://www.youtube.com/watch?v=R9OHn5ZF4Uo)

对于伟大的图片和可怕的视频在这个话题上有更好的深度和理解，一定会去看看！