# 构建我的第一个虚拟现实游戏——我从艰难的道路中学到的 10 个教训

> 原文：<https://dev.to/jamesvidler/building-my-first-vr-game-10-lessons-i-learned-the-hard-way-17cf>

首先，我绝不认为自己是游戏开发或虚拟现实方面的专家。我是一名对虚拟现实充满热情的网络开发人员，我的目标是用 Unity 发布一款虚拟现实游戏。在为 Oculus Go 创作和发布 [Dodgeblock VR 之前，我没有任何游戏开发经验。](https://www.oculus.com/experiences/go/2276532692364976)

[![Alt Text](img/159ffe653fe6acd8f95dbcf72aef9d76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--op7h60i3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jfab1s3a5mxs26m82wes.png)

容易吗？不。值得吗？是的，这是一次非常有益的经历。

然而，我真希望在开始这一切之前，我就知道我在做什么。如果我知道我现在所知道的，我就可以为自己节省很多时间和精力。这些都是给我年轻(头发也更浓密)的自己和其他所有想开发虚拟现实游戏的创意灵魂的教训。

## 第一课——不要构建游戏

这听起来事与愿违，但是如果你刚刚开始使用 Unity 和 C#，你什么都不知道。

[![Alt Text](img/8ed46e8baaeff69f9344ae9789e0840d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_vFDjUM5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8atrh68wkminff2o1c5x.gif)

尽可能多的花时间做指导、练习和实验。如果你心中有一个游戏，然后你马上开始尝试去实现它，你会陷入困境，意志消沉。

当学习新事物时，停滞不前是过程的一部分，但随着游戏开发，这是一个完全不同的层次。

当你脑子里有了这个宏伟的想法，却连如何改变一个游戏物体的颜色都不知道的时候，你的目标似乎会比以往更加遥远。不要试图从一个游戏开始，而是拥抱实验。从教程或现有项目开始，并对它们进行修改。这将迫使你理解它们是如何工作的，并向它们学习。你最终会把它们完全弄乱，但没关系，它们本来就不是你的。

## 第二课——保持简单，笨蛋！

当你对 Unity 有了一些熟悉，你不能再让那些声音在你的脑海里保持安静，你会渴望开始一个游戏。是时候放松一下，瞄准天空了吗？不。瞄准低点。瞄准低点。

无论你认为你对 Unity 有多熟悉，使用它，并围绕你的优势设计你的游戏。当然，你可以走出你的舒适区，但在你走得太远之前，把自己带回安全的地方。

你有没有注意到 [Dodgeblock VR](https://dodgeblockvr.com/) 只由原始立方体游戏对象组成？

哦，我还尝试了其他的东西…我花了整整八个小时试图弄清楚如何以一种逼真的方式移动一个球，就好像它被抛向空中一样。然后，有一个错误导致了大量的虚拟现实球砸在我的脸上。

[![Alt Text](img/b3f148eec414eb681dbf553f40593105.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v09xMA1e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g52lbhgematzsw73m8c1.gif)

最终，我意识到我需要根据我的技能和舒适度，用一套规则来约束我的游戏，限制我的范围:

1.  使用由 Unity 提供的立方体基本游戏对象做几乎所有的事情。这对我来说是一个安全的赌注，因为我知道这些将在我的场景中表演——特别是因为我将在程序上产生它们。

2.  只针对 Oculus Go 平台。试图开发一款适用于多个平台的游戏并不容易，Oculus Go 非常适合我，因为我可以将耳机带到任何地方并分享我的游戏。以后会有时间将它移植到其他平台。

3.  没有多人游戏。打造一款优秀的单人游戏已经够难了。

4.  它必须简单，任何类型的虚拟现实用户都可以使用，即使是第一次使用。

5.  没有重力。物理很难。

[![Alt Text](img/473775addefa510597606924b7796c68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VjzIoZDD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p6fof9b3aancktgg40nb.gif)

一旦我做到了这一点，想象我能做什么和关注什么就变得容易多了。

## 第三课—尽快加入虚拟现实社区，不要停止

为了让你的游戏在商店里获得成功，你需要一些动力。你需要炒作，你需要为你担保的冠军。幸运的是，虚拟现实社区非常热情，渴望帮助你的游戏。不要等到你差不多准备好了才和别人分享你的游戏。早点做，经常做。在 Reddit 和 Discord 频道上建立粉丝群。使用 Oculus 中的预发布通道向用户提供您的游戏，在每次更新后通知用户，并保持一致。

我在 2019 年 2 月开始营销我的游戏，就在我计划发布它之前。我产生了一些兴趣，并保持了一段时间，得到了一些有用的(和一些不那么有用的)反馈，但我最终推迟了发布(参见第 7 课——功能蠕变是真实的),失去了我所有的动力。直到准备发布的前一周，我才开始再次吸引用户。我甚至完全忘记了不和谐。

不要忘记社区，他们是你成功或失败的关键。

[![Alt Text](img/92fe406e547506c50c97e1e1243b05ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y_xnHtRD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uh59wfqdi91j6xz2id8q.gif)

## 第四课——避开资产商店

很容易在资产库中搜索一些您不知道如何做的东西，然后将它导入到您的项目中。你觉得这是灵丹妙药吗？远非如此。这并不是说里面没有什么好东西，但是在你真正掌握你要进口的东西之前，你最好避开它。省下你将要花在一个拥有奇特粒子效果的套装上的 30 美元。它在演示场景中可能看起来很棒，但你不知道如何在你的场景中正确配置它，更不用说让它在 VR 中工作了。

## 第五课——休息

在一个项目上一次投入几个小时甚至几天是很容易的，尤其是当你对它充满激情和兴奋的时候。但是一次做太多会影响你的判断。你需要时间来反思你的工作，收集反馈和重复。

我把整个周末都花在了那些没有价值、被扔掉的东西上。

## 第六课——厚脸皮

所以，你现在有了一个游戏，不管它有多简单，它很有趣，你已经准备好和别人分享它了。恭喜你走了这么远！在某个地方有一个 Git 墓地，那里有数百万个从未公开的项目。现在是时候炫耀一下了！

当你向朋友、家人和测试者展示你的游戏时，他们会有自己的看法。他们不会总是和你的一致。获得反馈是非常有价值的，但也可能是你失败的原因。我们都知道负面反馈会带来伤害，但是如果你的游戏真的那么糟糕，现在知道总比以后知道好。从地板上站起来，休息一会儿，然后带着清醒的头脑回来。

那么，积极的反馈呢？半信半疑地接受你的朋友和家人的积极反馈，他们会支持你的。你能真正信任的唯一积极的反馈来自独立的测试人员。在可能的情况下，使用一组你个人不了解的测试人员。

## 第 7 课—特征蠕变是真实的

对我来说，最难理解的反馈类型是特性请求。这听起来很棒，通常是人们喜欢你的游戏的好迹象。事实上，他们非常喜欢你的游戏，他们投入了自己的时间来帮助你。你可能会在这里得到一些很棒的想法，但你需要总是把它带回到第二课——保持简单，愚蠢的。

实现你的社区所要求的一切是很诱人的。你想让他们开心。毕竟，他们是你的第一批顾客。但是，如果你不小心的话，你可能会把你游戏的范围扩大到你从来没有想到的水平，扭曲了你对游戏的最初设想。在实现特性的几次迭代之后，您可能会得到更多关于这些新更新的反馈，并且您的任务列表将会继续增长。你将会过了预定的发布日期，由于游戏的复杂性，维护将会变得更加困难。更多的错误，更多的麻烦，现在你有了一个滚动的发布日期。

[![Alt Text](img/90ba305f07fcaf5684f86e3836c88e14.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SXp2kjym--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ev5pdypmmgsw6o64sr4.gif)

是的，这发生在我身上。我让 Oculus 批准了 [Dodgeblock VR](https://dodgeblockvr.com) ，并准备在 2019 年 2 月推出。同时，我刚刚从我的测试人员那里收到了大量的反馈。我太兴奋了，人们真的在玩我的游戏，我把自己献给了他们。我会处理每一条反馈，并努力满足他们的要求。最终，这迫使我在游戏上市前就把它从 Oculus 商店撤了下来——担心它不够好。在过去的几个月里，我来回尝试实现不同的东西来满足用户。大多数时候，这导致我构建一些东西，测试它，然后在一周左右的时间里由于性能和稳定性问题而放弃它。6 个月后，我终于知道，如果我想推出我的游戏，我需要把它拨回基础。

## 第八课——留意你的伴侣

开发一个视频游戏需要大量的精力和时间。如果你和我一样，你有一份全职的技术工作(我管理一个 SaaS CMS 产品——Agility CMS ),当你回到家的时候，这份工作不会总是让你的油箱里剩下很多。如果你有一个伴侣和你住在一起，而你把所有剩余的精力都花在了他们不能参与的事情上，这将会影响到他们。当然，他们会支持你，他们可能永远不会告诉你这让他们很困扰，但这会慢慢耗尽你在他们那里的情感银行账户。

不要让你们的关系紧张，不值得。努力和你的搭档安排好相处的时间，并试着为你的游戏设定一个时间表。这将有助于你们充分利用在一起的时间，并设定期望。如果可以的话，试着让你的伴侣也参与到你的项目中来。或许他们可以帮你测试、制作营销材料，如截图、海报、视频等。对我来说，我从我的合作伙伴那里获得了一些支持，因为我确定我的游戏的任何收益都将捐给我们的“艺术基金”。不知道我们会多快到达那里，但她现在在我的角落里；)

## 第九课——移动 VR！=电脑虚拟现实

如果你是做移动 VR 平台开发的，这个也适用。当你在 Unity 中开发时，一些东西在你的 Oculus Rift 上工作得很好，并不意味着它在 Oculus Go 上也能工作得很好(或者根本不能工作)。Go 拥有与 Rift 截然不同的硬件，每一帧都是移动中的关键。在旅途中密切关注 FPS。添加一个 FPS 计数器，以便在移动测试时可以看到 FPS。如果它低于 60 帧/秒(平均)，恢复您刚才所做的任何更改。

我无法告诉你，由于移动中的性能问题，我浪费了多少时间来恢复一天(有时是几天)的更新。更糟糕的是，我经常测试一大堆更新，然后我不得不经历艰苦的排除过程来找出罪魁祸首。在每个 bug 修复或特性更新之后，在继续之前进行测试，并且聪明地使用 Git 来跟踪这些变化。

## 第十课——你能做到！

如果你不熟悉冒名顶替综合症，在尝试制作你的第一个游戏后你就会熟悉了。你会一直认为你或你的游戏不够好。你会怀疑每一个决定，失去信心，甚至可能放弃。

对付这种情况的最好方法是拥有一个良好的支持系统。朋友和家人对此很有帮助。事实上，你正在开发一款虚拟现实游戏，这已经足够了不起了。当你沮丧的时候，和他们谈谈。有时候你需要的只是一点安慰和一个提醒，让你知道自己有多棒。

虚拟现实社区也可以在这方面提供帮助。没有什么比听到有人在我的游戏中迷失了自我更让我振奋的了。

[![Alt Text](img/1305b4464b2c5ae0bb77cc6e57867dd3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YFW8rUVd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1a9pb9xhqq4bh771p9od.gif)

## 发布后的期待

Dodgeblock VR 于 2019 年 8 月 23 日推出，也是我开始写这篇帖子的同一天。在发布日，我有一种巨大的成就感，但我也非常紧张。这是美国东部时间下午 2:30，游戏已经推出了一个半小时，在发布日零销售的想法开始出现。尽管有来自社区的兴趣和对产品的信心，我还是焦急地每隔几分钟刷新一次 Oculus analytics 仪表盘。又过了 30 分钟，仪表盘刷新了，上面写着“售出 1 本”！

我欣喜若狂，它只意味着 2.00 美元并不重要，这从来都不是为了赚钱。我想做的就是创造一个我能享受的游戏，并且希望其他人也能享受。人们为此买单的事实让它变得更加甜蜜。

在周五，周六，现在周日的过程中，我看到了稳定的销售和非常积极的评论。现在还为时尚早，还在加速发展，但我希望它能成为 VR 社区中家喻户晓的名字。

现在，我要去查看 Oculus 仪表盘，并按几次 F5 键…