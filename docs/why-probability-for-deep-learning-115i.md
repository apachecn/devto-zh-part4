# 深度学习为什么要概率？

> 原文：<https://dev.to/mmithrakumar/why-probability-for-deep-learning-115i>

与计算机科学家和软件工程师的世界不同，在那里事情完全是确定的和确定的，机器学习的世界必须总是处理不确定的量，有时是随机的(非确定的或随机确定的)量。

不确定性有三个可能的来源:

1.  固有的随机性:这些系统具有固有的随机性。比如使用 python rand()函数，它在每次运行时输出随机数，或者量子力学中的亚原子粒子动力学，它在量子力学中被描述为概率。

2.  不完全可观察性:最好的例子是蒙蒂霍尔问题，电影《21 世纪吉姆·斯特吉斯》中的问题，有三扇门，一扇门后有一辆法拉利，另外两扇门通向一只山羊。观看[场景](https://www.youtube.com/watch?v=cXqDIFUB7YU)了解如何解决天魔堂问题。尽管参赛者的选择是确定的，但从参赛者的角度来看，结果是不确定的，当你不能观察所有的变量时，确定性系统看起来是随机的。

3.  不完整的建模:剧透警告，在这一点上，我怀疑它是一个剧透！好吧，在游戏的最后，当钢铁侠赶走了所有灭霸的部队，(我知道，仍然从现场恢复)，我们想知道加莫拉到底发生了什么，她被赶走是因为她最初和灭霸的部队在一起，还是因为她背叛了灭霸而得救。当我们丢弃一些关于模型的信息时，丢弃的信息在这种情况下，托尼是否知道加莫拉是好是坏，导致模型预测的不确定性，在这种情况下，我们不确定她是否还活着。

好吧，发誓，最后的复仇者联盟参考。

[![frequentist probability](img/8b37013d084dec55c9f61131dee88f82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l0hhQYCo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/adhiraiyan/DeepLearningWithTF2.0/master/notebooks/figures/fig0300a.jpg)

当奇异博士说我们有一千四百万分之一的机会赢得这场战争时，他实际上看到了那一千四百万个未来，这被称为**频率主义概率**，它将一个事件的概率定义为它在大量试验中相对频率的极限。但我们并不总是有奇异博士的时间石来看到所有可能的未来或可重复的事件，在这种情况下，我们转向**贝叶斯概率**，它使用概率来表示对某些事件的信任程度，1 表示绝对确定性，0 表示绝对不确定性。

尽管频率主义概率与事件发生的频率有关，贝叶斯概率与定性的确定性水平有关，但我们将它们视为行为相同，并使用完全相同的公式来计算事件的概率。

* * *

这是《用 Tensorflow 2.0 进行深度学习》一书《用 Tensorflow 2.0 进行概率和信息论》一章的第一节。

您可以阅读本节和以下主题:

03.00 -概率与信息论
03.01 -为什么是概率？
03.02 -随机变量
03.03 -概率分布
03.04 -边际概率
03.05 -条件概率
03.06 -条件概率的链式法则
03.07 -独立性和条件独立性
03.08 -期望、方差和协方差
03.09 -常见概率分布
03.10 -常用函数的有用性质
03.11 -贝叶斯' 1

在[用 TF 2.0 深度学习:03.00-概率论与信息论](https://www.adhiraiyan.org/deeplearning/03.00-Probability-and-Information-Theory)。你可以在这里获得这篇文章和本章[其余部分的代码。Google Colab 和 Jupyter Binder 中笔记本的链接在](https://github.com/adhiraiyan/DeepLearningWithTF2.0)[笔记本](https://www.adhiraiyan.org/deeplearning/03.00-Probability-and-Information-Theory)的末尾。