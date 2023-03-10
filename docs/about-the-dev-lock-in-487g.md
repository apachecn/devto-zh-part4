# 关于开发锁定

> 原文：<https://dev.to/matks/about-the-dev-lock-in-487g>

如今，大多数(聪明的)公司都知道他们应该避免[厂商锁定](https://en.wikipedia.org/wiki/Vendor_lock-in)。

然而，我仍然看到许多公司允许开发者锁定，尽管这几乎和厂商锁定一样糟糕。

### 什么是 dev 锁定？

根据维基百科，“供应商锁定[...]使客户依赖于某个供应商的产品和服务，如果没有可观的转换成本，就无法使用另一个供应商。”开发者锁定对开发者来说也是一样的:它使得公司在项目和行动上依赖于一个人(开发者),如果没有巨大的转换成本，就无法要求另一个开发者去做。

有多种情况可以被认为是开发人员锁定，但最常见的是“嘿，请见见史蒂夫。史蒂夫是这个关键项目的唯一开发人员。史蒂夫按照自己的标准、自己的逻辑(大多数人不理解)编写了代码库，从未编写过任何文档或自动化测试，是唯一知道如何部署的人。”

大多数时候，你可以在故事中加入“有趣”的部分:

*   50%的部署会失败并导致停机，不可能进行备份或回滚，Steve 在服务器上手动执行这些操作
*   密码？哦，在服务器和史蒂夫电脑上。什么？版本控制工具？饭桶？这些外来词是什么？
*   我们每天是如何工作的？嗯，我们告诉史蒂夫我们想做什么，他说“好的，完成”，然后我们检查生产是否运行良好。什么？“暂存服务器”？没听说过，没有。

但故事中最悲伤的部分是，史蒂夫通常并不是一个坏人，他并没有意识到自己造成的束缚。史蒂夫刚刚接到一个对他来说太重要的项目，所以他尽自己所能去做。你猜对了:通常，Steve 实际上是一个初级开发人员，没有高级开发人员和他一起帮助他正确地构建东西。现在，该公司有一个开发锁定加上一个巨大的技术债务。

但是，开发人员锁定在公司中可能会发生，原因有很多:

*   在一个团队中，除了一个人(初级开发人员)之外，其他人都离开了，突然有这么多的工作，以至于“留下来”的开发人员不能正确地做事情，所以他无意中锁定了项目
*   该公司雇佣了一个“10 倍忍者魔术师开发者”，实际上是一个狂热的开发者，他说“测试是为弱者准备的”或者“快速行动，打破(每一个)东西！”
*   ...

### 贵公司应该如何避免开发锁定

开发人员锁定主要是因为缺乏沟通。代码库如何工作，如何部署，如何修改，如何存储。
很明显，对抗开发锁定的第一个工具是沟通。如果你是一名开发人员，*沟通意味着文档化*。

给新人写文档。为高级用户编写文档。为部署过程编写文档。记录以前的事故。写教程和例子。画图式，它们是很好的交流工具！
并分享一下。并确保定期更新。

其次，要有一个成员不止一个的团队，让他们切换项目。不要让一个人参与一个项目，“因为他更了解这个项目，所以如果我们把这个任务分配给他，他会做得更快”。这就是陷阱。即使你认为你的开发人员或同事是一个值得信任的人，并且如果有一天他决定离开，他会进行正确的知识转移，请记住，人是会生病的...或者死亡。是的，你没听错。有一家公司有一个开发人员锁定了一个开发人员，但确信这个人永远不会离开，因为他在那里非常开心。这家伙死于车祸。他死后的第二天，没人知道如何部署。所以请永远不要依赖一个人。总是让多个团队成员了解系统的关键部分。

你可以用来对抗开发锁定的第三个工具是使用标准。不要多此一举。如果你能使用被其他开发者广泛使用的东西，那就去做。如果你需要:使用标准框架，使用流行的工具，使用流行的 ORM 和库以及规范，这将使这些人参与项目变得容易得多。遵循流行的代码样式和命名约定。你的代码库越标准，如果你需要的话，就越容易让另一个开发人员加入这个项目。

这也适用于技术选择。比方说，你必须在 X 技术和 Y 技术之间为你的最新项目做出选择。

与技术 Y 相比，技术 X 可以给你 0.01%的性能提升。但 Y 是一项趋势技术，github 上有 12 个维护团队，周围的每个人都开始使用/实现它，而 X 由一个人维护，最后一次发布是在 2 年前...这里，你最好选择 Y，失去这 0.01%的性能，但享受标准技术的可靠性和安全性。

当没有人愿意接管项目时，也可能发生开发锁定，因为项目太老了，有太多的技术债务，或者使用了没有人知道/使用/喜欢的技术。是的，这很可悲，但趋势确实很重要。你的代码库或项目需要能够吸引和激励从事这项工作的人。否则，你会被困在现有的团队中，而这个团队总有一天会离开。

### 最后

我希望这篇文章对您来说是有趣的，并且将帮助您做出正确的决定，以避免您公司中的开发锁定。小心点，因为有时候...你是锁。是的，如果你是一个人在做这个项目，考虑让你的经理雇用其他人，这样你就不会被你工作的公司所束缚了:)。