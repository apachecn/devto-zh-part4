# 一年内的机器学习

> 原文：<https://dev.to/scrimba/machine-learning-in-a-year-j50>

[![](img/da31047d19148040561a7fab00f3c732.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oUnahYeM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Afy4mkPit0bgQJPrYlsX0mQ.png)

这是我之前写的一篇文章[一周内的机器学习](https://medium.com/learning-new-stuff/machine-learning-in-a-week-a0da25d59850)的后续，讲述了我如何通过花五天时间研究这个主题来开始我的机器学习之路。

在这个非常有效的介绍之后，我继续利用业余时间学习，几乎整整一年后，我在工作中完成了我的第一个 ml 项目，其中涉及使用各种 ml 和自然语言处理(nlp)技术来[鉴定](https://github.com/xeneta/LeadQualifier) [Xeneta 的销售线索](http://xeneta.com)。

这感觉像是一种祝福:做一些我平时为了好玩而做的事情，还能得到报酬！

这也让我摆脱了只有拥有硕士学位或博士学位的人才能在专业上与 ml 合作的错觉。

> 事实是，你不需要太多的数学知识来开始机器学习，你也不需要一个学位来专业地使用它。

在这篇文章中，我想分享我的旅程，因为它可能会激励其他人也这样做。

### 首先介绍:黑客新闻和 Udacity

我对 ml 的兴趣可以追溯到 2014 年，当时我开始在 [Hacker News 上阅读关于它的文章。我发现通过查看数据来教授机器知识的想法很有吸引力。那时我甚至不是一名专业开发人员，而是一名业余爱好的程序员，做过几个小项目。](http://news.ycombinator.com)

因此，我开始观看 Udacity 的监督学习课程的前几章，同时也阅读了我遇到的所有关于这个主题的文章。

这给了我一点概念上的理解，虽然没有实际的技能。我也没有完成，因为我很少完成 MOOC 的。

### Coursera 的 ML 课程不及格

2015 年 1 月，为了让[成为一名开发者，我加入了伦敦的](https://medium.com/learning-new-stuff/from-non-technical-to-hired-in-5-months-d010f601b1bc)[创始人和程序员](http://foundersandcoders.org) (FAC)训练营。几个星期后，我想学习如何实际编写机器学习算法，所以我和几个同事成立了一个学习小组。每周二晚上，我们会看来自 [Coursera 的机器学习](https://www.coursera.org/learn/machine-learning)课程的讲座。

这是一门奇妙的课程，我学到了很多东西。但是对初学者来说很难。我不得不一遍又一遍地看讲座，才能理解其中的概念。Octave 编码任务也很有挑战性，尤其是如果您不了解 Octave。困难的结果是，随着时间的推移，一个接一个的脱离了学习小组。最终，我自己也掉了下来。

事后看来，我应该从一门课程开始，要么使用 ml 库完成编码任务——而不是从头开始构建算法——要么至少使用我知道的编程语言。

> 对于一个新手来说，在学习一门新语言的同时尝试编写 ml 算法太难了。

如果我能回到过去，我会选择 Udacity 的《机器学习入门》,因为它更简单，并且使用 Python 和 Scikit Learn。这样的话，我们会尽快弄脏自己的手，获得自信，获得更多的乐趣。

[* *机器学习课程简介| Udacity](https://www.udacity.com/course/intro-to-machine-learning--ud120)

> 经验教训:从简单实用的东西开始，而不是从困难的理论开始。

### 机器学习一周

我在 FAC 做的最后一件事是 ml 周特技。我的目标是能够在周末将机器学习应用于实际问题，我设法做到了。

整整一周，我做了以下事情:

*   了解 **Scikit 学习**
*   在真实世界数据集上尝试 ml
*   从头开始编写一个**线性回归**算法(Python 语言)
*   做了一点点的自然语言处理

这是迄今为止我经历过的最陡的 ml 学习曲线。如果你想要更详细的概述，请继续阅读文章。

> 吸取的教训:花一周时间让自己沉浸在一个新的主题中是非常有效的。

### 失败的神经网络

我在伦敦完成 FAC 课程并搬回挪威后，我试图重复 ml 周的成功，但用的是神经网络。

这个失败了。

每天花 10 个小时来编码和学习有太多分心的事情。我低估了在 FAC 被同龄人包围的重要性。

> 吸取的教训:在做这些学习特技时，找一个欣欣向荣的环境包围自己。

但是，我至少对神经网络入门了，慢慢开始掌握概念。到 7 月份，我设法编写了我的第一个网络。这可能是有史以来最糟糕的实现，实际上我觉得炫耀起来很尴尬。但是它成功了；我向自己证明，我理解像**反向传播**和**梯度下降这样的概念。**

[![](img/acba8e8cb13af4a854fbaf91715775ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NKh7iPu2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2APcmDyGu5EtXGRxErI4mP7w.png)

下半年，我的进度慢了下来，因为我开始了一份新工作。这一时期最重要的收获是神经网络实现从**非矢量化**到**矢量化**的飞跃，其中包括重复大学的线性代数。

年底，我写了一篇文章，总结了我是如何在这篇文章中了解到这一点的。

### 测试卡格尔竞赛

在 2015 年的圣诞节假期，我再次获得了激励，并决定尝试 Kaggle。因此，我花了相当多的时间来试验各种算法，用于他们的[网站报价转换](https://www.kaggle.com/c/homesite-quote-conversion)、[奥托集团产品分类](https://www.kaggle.com/c/otto-group-product-classification-challenge)和[自行车共享需求](https://www.kaggle.com/c/bike-sharing-demand)竞赛。

[![Kaggle is a fun way to experiment with datasets and get feedback on your performance.](img/99564a15e68f4c14f1f3118403019ec7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fPN_M974--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AfXm3r4U9OMSliH1arivz8Q.png) 
Kaggle 是一种有趣的数据集实验方式，可以获得对你表现的反馈。

主要的收获是通过对算法和数据进行实验来迭代改进结果的经验。

我在做机器学习的时候学会了**相信自己的逻辑**。

如果调整一个参数或设计一个新特性在逻辑上看起来是个好主意，那么它很可能真的会有所帮助。

### 制定工作中的学习计划

2016 年 1 月回到工作岗位后，我想继续我在圣诞节期间进入的状态。所以我问我的经理，我是否也可以在工作时间花些时间学习，他愉快地同意了。

[如何在工作中制定学习计划](https://medium.com/xeneta/how-to-gain-new-skills-at-work-923bb088a352)

在这一点上对神经网络有了基本的理解，我想继续深入学习。

#### Udacity 的深度学习

我的第一次尝试是 Udacity 的深度学习课程，结果大失所望。视频讲座的内容不错，但对我来说太短太浅。

IPython 笔记本的任务最终太令人沮丧了，因为我花了大部分时间调试代码错误，这是扼杀动力的最有效方式。所以在工作中做了几次之后，我就放弃了。

为他们辩护，当谈到 IPython 笔记本时，我是一个完全的新手，所以它对你来说可能没有对我来说那么糟糕。所以可能是我还没有准备好这门课。

#### 斯坦福—面向 NLP 的深度学习

幸运的是，后来我发现了斯坦福大学的 cs 224d T1，并决定试一试。这是一门奇妙的课程。尽管这很难，但我在做习题集的时候从来没有调试过。

其次，他们实际上也给了你解决方案的代码，当我陷入困境时，我经常会看这些代码，这样我就可以反过来理解达成解决方案所需的步骤。

虽然我还没有完成它，但到目前为止，它已经大大提高了我在 nlp 和神经网络方面的知识。

然而这很艰难。真的很难。有一次，我意识到我需要比我更好的人的帮助，所以我联系了一个博士生，他愿意以每小时 40 美元的价格帮我解决问题，包括问题集和整体理解。这对我继续前进至关重要，因为他发现了我知识中的许多黑洞。

> **经验:**每小时 50 美元左右就能找到一个好的机器学习老师。如果你买得起，它绝对是**值得的**。

除此之外， [Xeneta](http://xeneta.com) 最近还聘请了一名数据科学家。他有数学硕士学位，所以当我遇到各种线性代数和微积分问题时，我经常向他寻求帮助。所以一定要检查一下你公司内部有哪些资源。

### 促进 Xeneta 的销售

做了这些之后，我终于觉得可以在工作中做一个 ml 项目了。它基本上包括训练一种算法，通过阅读公司描述来确定销售线索，事实证明，使用该工具可以为销售人员节省大量时间。

查看我在下面写的关于它的文章，或者前往 GitHub 直接进入[代码](https://github.com/xeneta/LeadQualifier)。

[利用机器学习提升销量](https://medium.com/xeneta/boosting-sales-with-machine-learning-fbcf2e618be3)

走到这一步无疑是一个漫长的旅程。但也是快速的；当我在一周内开始我的机器学习项目时，我当然不希望在一年内实际专业地使用它。

但这是百分之百可能的。如果我能做到，其他人也能。

感谢阅读！我的名字叫 Per，我是一个更好的教和学代码的方式的联合创始人。我们有大量关于网络开发的免费课程，所以如果你感兴趣的话可以去看看。

如果你已经读到这里，我推荐你去看看这个演示！