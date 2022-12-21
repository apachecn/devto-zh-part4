# 数据科学项目:机器学习过程步骤

> 原文：<https://dev.to/roksanatanov/data-science-projects-machine-learning-process-steps-4nel>

人工智能和人工智能技术在今天如此普遍，以至于公司感到有压力引入它们，否则它们会显得过时或落后于竞争对手。但是，仅仅渴望创新是不够的，并不是所有人都成功地收获了人工智能的全部潜力。许多公司只是缺乏工具或人工智能生命周期管理经验来开展他们的数据科学项目。

试图忽略 BI 和分析的关键实践，直接采用人工智能是许多人工智能爱好者的常见错误，也是导致灾难性失败的错误。那么构建人工智能产品和服务的正确生命周期是什么呢(顺便说一下，它不同于传统的 T2 软件工程生命周期 T3)？以及如何建立强大的 AI 基础？让我们讨论一下

## 可靠的数据流是所有数据科学项目的核心

熟悉[大数据技术](https://perfectial.com/blog/how-corporations-can-leverage-big-data/)并成功将其用于数据集成/ ETL、数据治理和其他数据服务的公司，为其未来的 AI 和 ML 项目奠定了坚实的基础。其他人需要先获得一些基本的 BI 和分析。以下是我们的分步指南:

### 第一步。规划

[![Data science projects - Planning](img/1beb0735b99273828f5e23db2edbb4a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LQne62hY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://perfectial.com/wp-content/uploads/2019/06/Data-science-projects-Planning.jpg)

首先，找出你希望人工智能技术关注的关键领域。一般的实践表明，在这个阶段最好让数据科学家参与进来，讨论你希望你的项目朝哪个方向发展。

您需要决定首先要自动化哪些任务。为了帮助你决定回答这些问题:

这个特殊的任务是数据驱动的吗？

自动化带来的规模真的值得付出努力吗？

*是否有足够的相关数据来支持自动化，是否足够干净，是否贴好标签？*

在这个阶段，清楚地陈述你的商业目标是很重要的，并确保你放在人工智能面前的任务是可以实现的。我们需要确定我们计划给我们的人工智能分配一个可用现有数据解决的问题。

### 第二步。数据审计

[![Data science projects - Data audit](img/f85feb930079f480e59275baadb7a7f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dmizAAFY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://perfectial.com/wp-content/uploads/2019/06/Data-science-projects-Data-Audit.jpg)

接下来，数据科学团队将开始对所有数据源进行编目，并研究*可用数据的干净程度，其与任务的相关性，是否有正确标记的训练数据集(或者是否需要进一步的注释)，或者分散在不同 MES、RIP、SCADA 平台上的数据(以不同格式)是否可以以某种方式连接。*

这时，您可能会发现丢失了大量相关数据，或者一些服务日志不一致且不可用。

数据清理将大大减少您的初始数据集，这意味着您可能需要重新考虑 AI 模型的目标区域。

### 第三步。挑选/创建特征

[![Data science projects - Picking](img/46825c6c6fdb4e841e49ed835cb9975a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oH2mju55--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://perfectial.com/wp-content/uploads/2019/06/Data-science-projects-Picking.jpg)

此阶段是数据科学团队和决策者之间的团队合作，旨在编制一份具有最强预测信号的功能列表。然后，数据科学家必须根据这些特征的计算难度来评估它们的预测潜力，并选择几个最佳特征开始实验。

### 第四步。系统模型化

[![Data science projects - Modelling](img/ac3b838ffe772a459e5d84b6cad49a92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T9f_dA0A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://perfectial.com/wp-content/uploads/2019/06/Data-science-projects-Modelling.jpg)

与工程类似，从简单的事情开始——构建一个基线模型，合并一些简单的特性，并从那里迭代。

这些常见且表面上简单的算法(逻辑回归、随机森林)是经常投入生产的算法；它们只需要调整几个参数，不需要太多的训练，而且在某些情况下，对过拟合有惊人的鲁棒性。

### 第五步。部署

[![Data science projects - Deploying](img/5f6af10e044537892ded56883d7b8800.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fPf48XDO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://perfectial.com/wp-content/uploads/2019/06/Data-science-projects-Deploying.jpg)

不要急着这么做。应该有一个合适的实验框架(甚至一些原始的 A/B 测试也可以),允许我们逐步部署并最小化风险，以及端到端地调试算法。

来自最终用户的反馈应该在开发的早期就被纳入进来，我们必须记住，原始的预测解决不了任何问题。我们总是需要一些后处理功能(API，工作流工具)来使模型的输出有用和可解释，以便公司知道是哪些因素在驱动它们。

然后，我们将最终开始开发一个更高级的网络应用程序；有时，使特征提取达到生产水平是一个耗时的过程，并且将模型包装到其他应用程序可以查询预测的软件包中需要大量的工程工作。

### 结论

人工智能不是魔法，它是应用统计学和线性代数。

你的模型会和你提供给它们的数据一样好。因此，如果你的数据库充满了不一致，有缺口，结构混乱，即使最先进的 ML 技术也不能帮助你得到想要的结果。

忽略所有数据科学项目的工程部分是一种常见的做法，因为它们不像建模那样令人兴奋。但你必须记住，你的人工智能努力的成功取决于你的公司生成和处理的数据的质量。

这个帖子最先出现在乌克兰 IT 外包公司 Perfectial 上。