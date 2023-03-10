# 算法靠回顾生存

> 原文：<https://dev.to/horia141/algorithms-to-live-by-review-35k4>

这是我对由 Brian Christian 和 Tom Griffiths 撰写的由生存的[算法的评论。这本书的目的是用有趣和中肯的例子突出一些问题和解决它们的算法。这显然是科普，所以不要指望任何沉重的数学-或任何数学。但这是一本有趣又轻松的读物，适合度假时阅读。不管你的技术水平如何，这些问题的历史框架都是很棒的。](https://www.amazon.com/Algorithms-Live-Computer-Science-Decisions/dp/1627790365)

第一章处理[最优停止问题](https://en.wikipedia.org/wiki/Optimal_stopping)，或者所谓的*秘书雇佣问题*。设置是你有一系列的选择，你必须选择一个最好的。通常情况下，你不能违背自己的选择。现实生活中有很多场景:买房子、找伴侣、雇人或者决定在停车位停车。每一个都给基本设置增加了一点变化。并且为每一个提供了简单的解决方案。例如，对于标准变量，如果你在看到 37%的选项后选择了你遇到的最好的一个，平均来说你保证有一个最优解。违反直觉，但有数学支持。

第二章处理[多武装土匪问题](https://en.wikipedia.org/wiki/Multi-armed_bandit)。这些也是选择问题，就像最优停止问题一样，只是我们必须做许多选择——通常比我们拥有的选项多得多。每个选择都为我们提供了一定的回报，但结果是随机的。此外，我们不知道结果分布，我们必须通过我们的选择来了解它。因此，我们在每一点上都面临着一个利用-探索的困境——选择我们知道会产生好结果的选项，还是选择回报更好的未尝试的选项。我们在生活中也会发现同样的问题——例如，当我们决定是去一家我们知道不错的餐馆，还是去一家我们没去过的餐馆时。在工业中，这样的例子甚至更多——大多数产品公司 A/B 测试他们的产品。并且 A/B 测试是一种多臂 bandit 的形式，其约束条件是我们总是同等地检查两个分支。事实上，单克隆抗体是统计测试的概括。这里涉及的两个算法是吉廷斯指数和置信上限算法，它们偏向于探索。心理学家在人类身上也发现了这一点。

第三章讨论排序问题。它实际上设法以非常好的形式向外行人展示了 Big-O 符号，然后快速连续地引入了冒泡排序、插入排序和合并排序。这一章的后半部分致力于体育排名，以及如何组织你的比赛的大多数结构-单淘汰制，括号等不足以捕捉球队的完整排序，但只能可靠地选择最好的一个。但即便如此，在许多体育项目中，弱队战胜强队的可能性还是很大的。并且提出了在比较器函数中使用随机性成分进行排序的想法，我觉得这个想法非常好。

第四章继续介绍标准的计算机科学主题缓存。它涵盖了现代计算机的内存层次和导致它们的权衡。然后它将它们与图书馆组织联系起来——这很合适，因为在这两种情况下，我们都在处理空间及其结构如何影响我们如何构建系统。这一章的最后一部分集中在人类的记忆，以及像认知能力下降这样的事情如何解释为没有好的缓存的信息检索。或者至少有着不同于我们当前世界的缓存。

第五章处理调度，特别是在一台*单机*上的调度，将它与人类在他们自己的日历和任务中遇到的问题紧密联系起来。这里最大的收获是将针对中断和抢占的操作系统设计与针对生产力的标准建议同步——长期关注一个主题，但在面对吞吐量时要权衡响应能力。

第六章是关于[贝叶斯法则](https://en.wikipedia.org/wiki/Bayes%27_theorem)的，非常有趣。重点介绍了常见分布的类型:正态分布、幂律分布和 Erlang 分布，以及它们如何实现 wrt Bayes 规则的 wrt 预测:首先假设取平均值，其次是乘法，第三只是预测常数。这一切是如何与人类的预测联系在一起，以及我们如何直观地学习和使用它们，这是非常巧妙的。我们如何对有好模型的事物做出好的预测，对没有模型的事物做出坏的预测。以及现代世界对新闻和事件信息的机械传播如何扭曲了我们的模型！

第七章是关于过度拟合。这在统计学和 ML 中是一个相当技术性的话题，但是作者设法把它作为一种在不确定性下进行决策的工具，相当体面地提出来。在这个过程中，他们涵盖了交叉验证、规范化和提前停止等技术。

第八章讨论最优化问题以及如何用松弛法近似求解它们。它涵盖了 tsp 和分配问题的实际应用，然后深入研究约束和连续松弛方法。拉格朗日松弛法，你把约束条件转化成昂贵的成本元素，也被提到了，这是我第一次听说它，它看起来很简洁。

第九章讨论随机性以及它如何帮助解决棘手的问题。它首先介绍蒙特卡罗模拟方法和估算π。然后介绍 Bloom filters(可以说它不是随机的，但在许多情况下都是如此)。最大的部分归功于困难问题的最优化方法以及随机性在这里是如何起作用的。模拟退火是这里的重点，整个领域被比作创造力和“跳出框框思考”。

第十章是关于网络。这可能是最远离现实生活的*应用*，主要是各种技术和协议的概述，最后是关于[buffer float](https://en.wikipedia.org/wiki/Bufferbloat)的说明。

第十一章也是最后一章是关于博弈论的。前一部让我们远离了人类的事务，而这一部却让我们直接跌入了阴沟。有趣的是，这里不需要类比或翻译计算机算法到人类问题，因为博弈论已经是关于这个的了！对我来说，一个很好的结果是[无政府状态的代价](https://en.wikipedia.org/wiki/Price_of_anarchy)，这表明对于某些问题(或游戏),行为者以局部方式理性自私地行动，可能离系统的全局最优值并不远。这反过来也适用于*的城市交通*，例如，在那里我们可能会看到通过自动驾驶和协调汽车仅改善了 30%的交通——这肯定不是我所期望的大规模改善。另一个有趣的见解是关于机制设计，以及如果我们期望参与者理性行动，一些游戏是如何有缺陷的。工作中的假期政策、竞争商店的营业时间以及红杉林的生长方式都是这方面的例子。这里的*解决方案*是有一个约束的外部执行者——某种改变游戏规则的东西，这样这种行为就不会发生。想想政府、监管机构等。

最后，这本书的结尾是关于*计算友善* -意识到我们在现实生活中面对的许多问题都是计算性的，我们可以通过使问题容易解决来帮助彼此。比如提供一个有限的选择，这样另一个人就只能说是/不是，而不是大量的选项。

总的来说，这是一本好的、有用的读物，我会推荐给更多的工程师、计算机科学家、数据科学家等等。大多数问题和算法我都已经很熟悉了，但是这本书为它们提供了大量的背景和设置，使它们更像 T2 人，更有用，而不仅仅是下次我想构建 A/B 系统时使用的工具。历史资料也很好，再次帮助了问题的背景和动机！