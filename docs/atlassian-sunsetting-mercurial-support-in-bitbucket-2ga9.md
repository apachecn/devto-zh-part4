# BitBucket 中的 Atlassian 日落汞支持

> 原文：<https://dev.to/michelemauro/atlassian-sunsetting-mercurial-support-in-bitbucket-2ga9>

*(没想到在这里用这样的文字开始写作。但由于我在寻找比 twitter 上更长的观点，我认为这是一个很好的起点，也是一个很好的话题。)*

我通过 whatsapp 从一位前同事那里看到了标题:[Atlassian sunsetting mercurial support in bit bucket](https://bitbucket.org/blog/sunsetting-mercurial-support-in-bitbucket)。那真是一个沉重的打击。

对于那些不知道的人来说， [Mercurial](https://www.mercurial-scm.org/) 是第一个真正吸引人的真正分布式版本控制系统之一:它开始于 2005 年，现在仍在大量使用:Mozilla、Vim 和 OpenJDK 是一些备受瞩目的项目，它们承载了多年的开发，以及 hg 库中数百万行代码。

**在 git 成为主流之前，我就开始使用 Mercurial，**因为我和总部以外的一些同事一起工作:我们需要在相同的资源上合作，在这种情况下，我们无法接触到我们的中央简历。传递“无限量”提交的易用性、简单的命令以及几乎不可能丢失历史记录，使得在没有中央服务器的情况下，仅仅通过 skype 传递文件就可以工作数月。

它无法与当时(2007 年初)的 Git 相提并论:Linus 在几周内迅速完成的东西，不太稳定，仍然针对 Linux 用例进行了优化，并且敢于在它之外使用。Mercurial 已经很稳定了，在 windows 上也得到了很好的支持，有一个清晰的、经过深思熟虑的、易于理解和使用的模型。

**十二年后，** git 仍在[发布新命令，以阐明和简化一些用例](https://www.infoq.com/news/2019/08/git-2-23-switch-restore/)，而 Mercurial 发布的是关于[技术更新和新特性](https://www.mercurial-scm.org/wiki/WhatsNew)。你可以很容易地找到(并且可能需要)大量关于如何使用 git 以及如何理解其特殊概念的教程(比如[当然是 DEV](https://dev.to/unseenwizzard/learn-git-concepts-not-commands-4gjc) 上的优秀教程)；与此同时，我接触到的每一位 mercurial 同事从来不需要任何特殊培训，也从来不会因为头脑不清醒或被推错部门而丢了工作，不管他们以前的专业水平如何。

**最后，在过去的三四年里**,“GitOps”方法推动了 Git 集成的指数级增长，而我们现在却被困在了 VHS 领域。当然，Atlassian 有一个底线来检查，并最终得出结论，BitBucket 支持一个 VCS 太多了，由“1%的用户”使用的那个必须被淘汰。具有讽刺意味的是，BitBucket 成为 Mercurial 的第一个商业解决方案，这当然只是给这个故事增加了悲伤。

Atlassian 提出的选项确实令人印象深刻:一个尚未实现的自动转换服务，一些自托管指针([七足动物](https://heptapod.net/blog.html#first-article)和[卡莉西亚](https://kallithea-scm.org/)似乎是最有趣的；但它们与拥有 10 年以上经验的商业服务(或自己动手)相去甚远。**并且不要期望保留 PRs、评论、问题、元数据:**没有它们的导出路径，也没有项目结构的转换。当 Atlassian 在 2020 年中期拔掉插头时，它们将会消失。

这是一个悲伤的故事，将损害一个真正好的项目，一个真正的 VCS 空间的 Betamax。

然而，我想感谢最初的 BitBucket 团队，他们在 2008 年相信 Mercurial 并开始了它的旅程。亚特兰蒂斯虽然可以理解为一个商业实体，但仍在努力执行这些重大举措，同时尽量减少负面宣传，处理更糟糕的业力；模因作家们将会有更多的事情可以取笑。

**当然，这里的主要教训是**，第 n 次，**小心你放在云服务上的东西**。你呢，你有什么云服务？如果您当前的提供商停止提供服务或部分服务，您将能够卸载、重用和回收关于您项目的哪部分信息(问题、讨论、决策、权限、人员)**？**