# TPP 话题四:石头汤和水煮青蛙

> 原文：<https://dev.to/steadbytes/tpp-topic-4-stone-soup-and-boiled-frogs-4nla>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/the-pragmatic-programmer-20th/topic-4-challenges/)

> 见实用程序员 20 周年纪念版系列[第一帖](https://dev.to/steadbytes/the-pragmatic-programmer-20th-anniversary-edition-series-1e2l)介绍。

## 挑战 1

> 在审查第一版的草稿时，约翰·拉克斯提出了以下问题:士兵们逐渐欺骗村民，但他们催化的变化对他们都有好处。然而，通过逐步欺骗青蛙，你正在伤害它。当你试图催化变革时，你能确定你是在做石头汤还是青蛙汤吗？这个决定是主观的还是客观的？

虽然不一定容易，但我认为我们可以确定催化变化是导致石头汤还是青蛙汤，这在很大程度上取决于变化的意图 T2。

*   石头汤
    *   改善其他相关人员的处境
        *   例如，降低项目成本，提高生产率
    *   帮助朝着目标前进
        *   例如，新功能发布
*   青蛙汤
    *   让其他相关人员的处境变得更糟
        *   介绍“CV 驱动开发”的新技术
    *   阻碍目标的实现

困难在于这个决定可能是主观的，也可能是客观的。例如，
通过放弃托管服务(如 AWS Fargate)来降低项目成本可能是企业的胜利(石头汤),但由于所需工作的增加(青蛙汤),它可能会对工程产生不利影响。为“CV 驱动开发”引入新技术会给项目中的其他工程师带来不必要的认知开销，如果技术不稳定，还会带来问题(青蛙汤)。然而，如果引入了对项目真正*有用的*特性，它可能会是一碗青蛙汤。

在促进变革时，我认为非常重要的是**清楚而诚实地**确定变革的*理由*。在整个过程中重新评估变革的影响也是至关重要的。如果士兵们在制作石头汤的过程中意识到村民贡献了他们食物供应的很大一部分，那么停止要求更多的配料将是明智的。

## 挑战 2

> 快看，你头顶的天花板上有多少盏灯？房间里有几个出口？多少人？有没有什么断章取义，看起来不属于的东西？这是一个情境意识的练习，一种从男女童子军到海豹突击队队员海军都在练习的技巧。养成真正观察和注意你周围环境的习惯。然后对你的项目做同样的事情。

现在我不是海军海豹突击队，但我确实骑自行车，这(为了安全)需要高度的情况意识。例如:

*   我后面有车吗？
*   我前面有辆车吗？
*   路面是什么样的？
*   路边有行人吗？
*   路上有行人吗？

对于软件项目，情景意识略有不同:

*   软件是否满足其要求？
*   靠谱吗？
*   是否可维护？
*   是否过于复杂？

我定期进行一些练习来帮助实现这个目标。

#### 写一份自述/项目概述

在不查看现有资源的情况下，撰写自述文件/项目概述需要能够
描述项目的目的、实施和设计决策。如果这变得很难做到，项目的质量可能会下降。

#### 重新安装项目

从您的开发机器上卸载项目(以及任何依赖项)。现在，只使用现有的安装文档重新安装项目(如果它不存在，那么将其作为本练习的一部分！).做起来有多容易？项目是否增加了难以管理/安装的额外依赖性？

#### 实现并测试一个新的“虚构”特性

想一个新的特性添加到项目中。它的范围应该足够小，不会占用大量时间，但也不是完全无关紧要。用足够的测试覆盖率来实现这个特性，以保证这个特性能够正常工作。实施起来有多难？测试有多难？