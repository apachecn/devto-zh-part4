# 我和我的团队在改进现有代码库中的代码质量时学到了什么

> 原文：<https://dev.to/sourcelevel/what-my-team-and-i-learned-when-improving-code-quality-in-an-existing-codebase-b5d>

在这篇文章中，我给出了一个学习列表和一个通用的过程来帮助你获得更好的代码质量。我在这里描述的想法是基于我的经验，并不代表获得一个好的源代码库的唯一方法。万一你不知道从哪里入手，希望这篇博文能帮到你。

## 代码质量改进的心得

在这个行业的这些年里，我和许多不同业务领域的公司一起工作过。然而，我注意到一些前提在所有的前提下都是一样的。在这里，我把它们作为学习来展示。在定义改进过程时，我非常重视这些要点；否则，成功提高和维护代码质量的机会就很渺茫。

*   这不是一个人的工作。真实。团队中的每个人都必须致力于改进现有代码，更重要的是，为新功能编写高质量的代码。
*   重构时冻结新特性是行不通的。当你的团队暂时没有交付任何东西时，业务或产品人员倾向于确保*重构*变成一个诅咒语，就像[胡言乱语](https://en.wikipedia.org/wiki/Abracadabra)或[骗人把戏](https://en.wikipedia.org/wiki/Hocus_pocus_(magic))。
*   **现在不是“快速行动，打破常规”心态的合适时机**。大的重构和架构变化已经有了更大的打破事物的潜力；走得快只会增加你的机会。专注于改进的速度，并确保*你得到的自动化测试越多，你得到的改变就越舒服*。在没有单元测试、验收测试和集成测试的情况下更改产品代码是非常危险的。记住，在重构过程中插入太多的 bug 也可能让这个词受到诅咒。
*   追踪战略，让你的团队保持一致。这是持续提高你的代码质量的最好方法。不时地，停下你正在做的事情，检查指标，改变策略(如果需要)，调整你的团队，继续工作。从你的错误中学习，并改进这个过程。
*   使质量改进变得自然。这是一项永无止境的工作。如果您想要创建这种文化并使之持久，我强烈建议将实践整合到您的开发流程中。
*   **不要比较。每个团队都有自己的问题和速度。**比较“进步分数”是一种不好的做法。环境变得越来越紧张，质量改进的努力很快就变成了降低一个指标的努力，以便在排行榜上名列前茅。不同的团队对质量有不同的看法，并且可能处于不同的环境中。将你的团队与同一个团队进行比较。"*团队有进展吗*"这才是重要的问题。
*   **放眼短期和长期**。选择策略时，不要只选择大的工作项目。它扼杀了你的动力，因为显示它的进步可能需要一些时间。从短期(低挂水果和小修补程序)和长期考虑。

记住它们，这里有一个关于如何处理混乱代码以及如何把它提升到一个更高水平的建议。

## 处理质量差的代码

### 1。衡量您当前的代码质量

你告诉我你的代码质量很差。然而，是吗？你不测量一下就知道了。此外，它还允许您查看更改的进度。他们是在提高代码质量，还是只是增加了更多的复杂性和代码味道？

如果你不知道用什么方法，我在上一篇博客文章[中建议了 3 个你可以通过 linters](https://dev.to/sourcelevel/measuring-code-quality-3-suggestions-on-how-to-start-and-why-they-are-important-35ee) 自动收集的方法。你可以自己运行它们，或者使用像 [SourceLevel](https://sourcelevel.io) 这样的产品来帮你运行。你自己看着办吧，只要你去衡量。

### 2。追踪改进策略

当团队中的每个成员都独自工作时，你就失去了对改进进度的跟踪，并一直感觉一事无成。这就是为什么你需要一个好的策略，因为它能给团队聚焦。

一个好的策略可能因业务和产品阶段而异。如果你在一家初创公司工作，还没有推出你的产品，你需要不断开发新功能和修复错误，以实现上市日期。通常，产品的这一阶段会引入许多气味和设计问题。不过，这没关系，因为你对你的产品不是很了解。你正在学习这个领域，需要时间来了解你的业务的全貌。在这个阶段，我认为最好的策略是引入尽可能少的问题，并跟踪现有的问题。

如果你的产品已经发布并运行，你可以发现所有这些代码的味道，计算方法和类圈复杂度，重复的代码，等等。然后，您需要将这些改进合并到产品进化计划中。

正如我所说，不要停止交付是我学到的东西。重构什么是真正重要的，什么对你近期的工作和业务需求有影响。它就像任何其他技术债务一样工作，必须包含在或稀释到路线图中。

当我说你应该稀释到路线图中，我的意思是不要超过一个董事会。根据你的业务需求，对你的董事会进行优先排序。通过为你的冲刺选择的卡片插入低挂的水果和小的变化。只要对你的业务有意义，重构和重大改变可能会导致独立卡。

对你的团队透明，优先考虑那些使未来工作更容易和更快改变的问题。如果解决一个问题在接下来的几周内没有明显的收获，就让它过去吧。请记住，你不仅仅想减少质量问题的数量，你还想不断提高你的质量。

与你的团队交流，向他们展示改进的价值。让你的产品和业务人员参与进来，了解成功和失败。通过使用他们的语言来展示它是如何影响他们的:交付更快，更少的错误，更好的性能，以及节省或获得的金钱。

### 3。不时地，与你的团队分享结果

正如我所说，这不是一个人的工作。因此，分享您的代码库改进的进展对保持您的团队参与和积极性都有积极的影响。可见性对于其他利益相关者也很重要，比如产品或业务人员，甚至是 CTO。

不管你是经理、技术主管还是开发人员。召集所有人庆祝小胜利，最重要的是，庆祝大胜利。确保每个人都参与到这个过程中来。

你可能会问，我应该何时以及如何分享进展？

每个团队可能会找到一个合适的机会。然而，这里有一些建议:你可以在评审会议上花 10 分钟浏览度量标准(对于那些运行 Scrum 的人来说)，在站立会议上每周花 3 分钟跟进，或者甚至每月单独开一次会议，仅仅是为了交流数字和调整后续步骤。会议之间的时间取决于变化的速度。你看到的进步越快，两者之间的时间就越短。

在回答如何分享进展时，我建议绘制带有度量结果的图表，它们是历史。它们是用数字讲述故事的最有效方式。仅仅通过看一个图表，你就可以知道你的团队是否做得很出色。此外，他们会帮你很多[询问关于你的过程的问题](https://dev.to/sourcelevel/measuring-code-quality-3-suggestions-on-how-to-start-and-why-they-are-important-35ee)。

### 4。不时地，回顾你的策略

随着您的团队成员继续在源基础上工作，他们会添加新的问题并解决旧的问题。保持您的度量标准与良好的策略保持一致是至关重要的。如果指标变化太大，是时候检讨你的策略了。

你的生意也可能会改变。如果你的竞争对手推出了一项新功能，而你的产品必须赶上他们，你可能需要在这方面更加努力。你可能不得不暂停一个重要的重构(以防特性不能等待它)，或者甚至讨论插入更多质量差的代码*和有更多时间改进它*之间的权衡。这是停下来制定新计划的最佳时机。不要放弃改进，即使你可能短时间内不会去做。

### 5。不时地，检查你的度量标准

卫生法规的另一个重要方面是定义什么是质量。如果你知道这一点，那么测量的工作就是告诉你它有多好。要实现这一点，你需要衡量正确的事情。

问题是，在我们开始衡量之前，我们无法确定什么是应该衡量的。这就是为什么收集新指标并停止收集不必要的指标是一项需要定期进行的工作。

## 结论

拥有健康代码库需要时间，开发流程必须作为过程的一部分进行集成。在这里，我介绍了一些经验(这样你就不会因为这些原因而失败)，以及一个你可以在你的公司或团队中实施的通用流程。

请在下面的评论中分享你的想法。

如果你不知道测量什么或者不想手动收集指标，可以查看 SourceLevel 的[支持的 linters](https://docs.sourcelevel.io/engines/) 和[注册 14 天的试用](https://app.sourcelevel.io/pricing)。

我和我的团队在改进现有代码库的代码质量时所学到的东西这个帖子[首先出现在](https://sourcelevel.io/what-my-team-and-i-learned-when-improving-code-quality-in-an-existing-codebase)[源代码级别](https://sourcelevel.io)上。