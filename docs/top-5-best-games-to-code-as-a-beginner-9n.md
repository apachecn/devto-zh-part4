# 为初学者编写代码的最佳游戏前 5 名！

> 原文：<https://dev.to/lukegarrigan/top-5-best-games-to-code-as-a-beginner-9n>

# 简介

编写代码没有比编写游戏更有趣的了，让东西在屏幕上移动是如此令人满意，简直太棒了。

因此，如果你是一个编程新手，或者你是一个从事企业系统编程多年的高级开发人员，这篇博客应该可以帮助你开始一些游戏开发，或者至少激励你去尝试一下。

简单说明一下，实际上我不是游戏开发人员，我的日常工作主要是编写大型的有进取心的 Java 应用程序。但是回家后花一个晚上写点更有趣的东西也是不错的。我在我的网站 codeheir.com上发布了一个我在这个博客中经常引用的教程系列，在那里我经历了游戏的演变，所以从 [Pong - 1972](https://codeheir.com/2019/02/04/how-to-code-pong-1972-1/) 开始，然后是[太空竞赛- 1973](https://codeheir.com/2019/02/10/how-to-code-space-race-1973-2/) 等等。这基本上是如何使用 [p5.js](https://p5js.org/) 编写游戏代码的一步一步的过程。当然，你用来编写游戏代码的语言并不重要，重要的是你从实际操作过程中学到的概念。

## 开始编码前

如果你已经知道用游戏开发语言编程的基本知识，我建议你在阅读我的任何一步一步的过程之前，先试着编码游戏。如果你陷入了困境，那么就用博客作为参考，看看你是否和我一样在用同样的方法解决问题，很可能你会找到更好的方法😅。

如果你完全是编程新手，那么我建议你看一看 Youtube 上的丹尼尔·希夫曼的《编码训练》( T1 ),他为初学者准备了一个精彩的系列。然后，当你开始理解基础知识的时候，就跟着我的博客去做你想编码的游戏。

[![Daniel Shiffman](img/83a1713dc346c71997744c1da0fa08ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m1suFinZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.giphy.com/media/l0HlGEX1ZORa0aIvu/giphy.gif%3Fcid%3D790b7611b3b543f39372b022606cd810930b308db14221bf%26rid%3Dgiphy.gif)

# 1- Pong

如果你在谷歌上搜索“创造的第一个游戏”,很可能会弹出 Pong，它不是正式创造的第一个游戏，但它是第一个商业上成功的游戏。它创建于 1972 年 6 月！

[![Pong](img/922d9d1ebf34b18dc24292e80c6348d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K_dj8S-v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codeheir.files.wordpress.com/2019/02/finished.gif%3Fw%3D1060)

编写 pong 代码会教你很多游戏开发的关键概念，这里有一些:

*   用户输入:播放移动桨和/或桨
*   击中检测:球击中球拍，然后
*   得分系统:你需要跟踪双方球员的得分
*   屏幕约束:防止挡板离开屏幕

关于如何编写 pong - [代码的一步一步的过程，请点击这里！](https://codeheir.com/2019/02/04/how-to-code-pong-1972-1/)

# 2 -太空竞赛

一年后，pong 推出了 Space Race(也是 Atari 出品)。太空竞赛非常简单，它是一个双人游戏，每个玩家控制一枚火箭。这个想法是为了在避开太空垃圾后到达地图的顶端以获得一分。中间的横条代表游戏的剩余时间，游戏结束时得分最多的玩家获胜！

[![Space Race](img/470ac5ab18f86530875b7511f48b7abf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--01AToeMT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codeheir.files.wordpress.com/2019/02/end-game.gif%3Fw%3D1060)

编码太空竞赛教给你一些关键概念，同时也巩固了你早先从编码 pong 中学到的一些概念:

*   使用数组:为了保存碎片，你可能会使用一个数组来迭代它们，并确定它们是否与火箭相撞
*   计时器:使用某种计时器来决定游戏何时结束

关于如何编码太空竞赛的一步一步的过程- [点击这里！](https://codeheir.com/2019/02/10/how-to-code-space-race-1973-2/)

# 3 架喷气式战斗机

喷气式战斗机是 1975 年发行的一款精彩游戏。很简单，有一架黑色喷气机和一架白色喷气机，黑色喷气机射出黑色子弹，白色喷气机射出白色子弹。目标是射击对方并获得一分。

[![Jet Fighter](img/d6633c6261b9d55f78d5483dcac72b57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xh_n7k37--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codeheir.files.wordpress.com/2019/02/scoring2.gif%3Fw%3D1060)

喷气式战斗机的关键概念:

*   屏幕环绕:当子弹/喷射离开屏幕时，它们会环绕并从另一边回来
*   射击:学习从你当前的角度发射子弹背后的数学原理

喷气式战斗机是我的多人太空游戏 **Spaceheir** 的灵感来源。这个想法是创造一个小行星和喷气式战斗机的混搭。以小行星的玩家与环境方面为例；射击小行星来获得关卡。以及喷气式战斗机的玩家对玩家战斗系统。

[![Spaceheir](img/7a49d5e23622f9fb31c1f641e7bd7067.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ol42SUCr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/LukeGarrigan/spaceheir/raw/master/stream-royale2.gif)

这个游戏是完全开源的，这里的 [github](https://github.com/LukeGarrigan/spaceheir)
一步一步地介绍如何编写喷气式战斗机- [点击这里！](https://codeheir.com/2019/02/24/how-to-code-jet-fighter-1975-4/)

# 4 -太空入侵者

到目前为止，1978 年发行的最受欢迎的游戏是《太空入侵者》。仅仅 4 年时间，它的票房收入就达到了 38 亿英镑，这一点无需赘述。

[![Space invaders](img/0a9e14a07fe3788291e267c731fae7b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hvsyJo7S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codeheir.files.wordpress.com/2019/03/done.gif%3Fw%3D1060)

编码太空入侵者的关键概念:

*   数组删除:删除或隐藏的外国人时，你开枪
*   阵列选择:确保只有底部的外星人在拍摄
*   难度:你玩游戏的时间越长，外星人变得越快
*   随机:给外星人一个随机射击的机会

关于如何编写太空入侵者代码的一步一步的过程- [点击这里！](https://codeheir.com/2019/03/17/how-to-code-space-invaders-1978-7/)

# 5 -摩纳哥大奖赛

摩纳哥大奖赛是 1979 年发布的一款无止境的赛车游戏。这是一个真正有趣的代码，无缝包装是什么使这个游戏如此重要，所以它感觉好像你是在一个无止境的赛道上比赛。注意，实际的游戏看起来和我的实现并不相似。

[![Monaco GP](img/e7bc4abb18c8f88483216aa68b216dc2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g661Vwt9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codeheir.files.wordpress.com/2019/03/collision.gif%3Fw%3D1060)

摩纳哥大奖赛的关键概念

*   永无止境的游戏:用一个很酷的游戏开发技巧让赛道不停的跑！
*   人工智能:编码其他汽车的行为，也让他们包装。

# 有人可敬地提到:

## 封锁

封锁是 Gremlin 在 1976 年发布的一款血腥辉煌的游戏，它基本上是 PVP 蛇，但发布了好 21 年前！

它包括编码一个蛇人工智能，这真的很有趣，也很困难。

[![blockade](img/a9849250abeb2f5bf3f192f21d02c9c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UD9DJkwn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codeheir.files.wordpress.com/2019/02/finished-2.gif%3Fw%3D1060)

## 峡谷轰炸机

峡谷轰炸机是一个有趣的老游戏。这不是最令人兴奋的，但它有一些有趣的编码功能，如如果下面的块被击中，使块下降，然后随后使块改变颜色，以匹配它现在的水平。

[![canyon bomber](img/14fe50f5bbb4c4206c9e7c82ed6a8933.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i_NZkzms--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codeheir.files.wordpress.com/2019/03/finished.gif%3Fw%3D1060)

# 然后呢？

当你觉得编写以上任何一款游戏都很舒服的时候，我建议你试着创建一款简单的多人/在线游戏。玩你自己创作的游戏很有趣，邀请你的朋友和家人到你的服务器上玩更有趣，这样你就可以拥有他们了🤣。我为任何想编写多人 p5js 游戏和节点的人创建了一个快速入门的 Github repo，这样你就不必重复大量的样板文件:[P5-多人游戏入门](https://github.com/LukeGarrigan/p5-multiplayer-game-starter)

[![P5 repo](img/90882bb0a6c14bf3d10b0bda2429037b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BHG4juiG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vAHPUf9.png)

# 谢谢

我希望你喜欢这个博客，也希望它能帮助到你们中的一些人。让自己参与一些游戏开发，这非常非常有趣。

如果你不想错过绝对精彩的编程洞察，请在 twitter 上关注我:🤣[@卢克 _ 加里根](https://twitter.com/luke_garrigan)

谢谢你，如果你喜欢我的漫谈，看看我在 https://codeheir.com/的个人博客网站

## 本博客由[代码画布](https://www.codecanvases.com)赞助

用市场上最酷的编程/编码画布让你的房间变得生动起来。[codecanvases.com](https://www.w3schools.com)是 100%独家设计画布的编程印刷的头号卖家。趁**优惠 20%,立即购买！！**
[![[https://codecanvases.com/](https://codecanvases.com/)](img/1e674e9e6a364911f77cb685d8b64139.png)T7】](https://res.cloudinary.com/practicaldev/image/fetch/s--qTjLHF_e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.shopify.com/s/files/1/0311/0151/7955/products/jqbr8djyof6ktl8qopmkpvy4_480x480.png%3Fv%3D1585483987)