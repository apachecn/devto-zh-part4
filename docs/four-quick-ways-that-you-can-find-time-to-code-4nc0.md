# 四种快速找到时间编码的方法

> 原文：<https://dev.to/teachingtls/four-quick-ways-that-you-can-find-time-to-code-4nc0>

在 Patrick Kua 的书[与技术领导者交谈](https://leanpub.com/talking-with-tech-leads)中，普遍认同的统计数据是，你应该将 30%的时间用于编写代码。在[之前的帖子](https://teachingtechleads.com/four-quick-ways-that-you-can-find-time-to-code/)中，我们讨论了你如何与你的开发人员步调不同，因为你真的不再是开发人员了。那么，你怎样才能找到时间来编码呢？

*[最初贴在 TeachingTechLeads.com 的](https://teachingtechleads.com/top-5-responsibilities-of-a-tech-lead/)*

不容易，我不骗你。我还没有掌握它，所以这是那些不断提醒我努力提高自己的帖子之一。这是我发现的四种方法，可以让我不用离开团队就能回到代码库。

### 搭建脚手架

这是最简单的方法。也是最无趣的。

在任何项目的开始，都会有一个时刻，你会想要建立项目结构。无论是手动完成所有事情，还是跑到 [Spring Initializr](https://start.spring.io/) 并点击几个复选框，它都会让你接近金属。

您可以为文件夹结构和命名约定注入您的个人标准，以及设置您的 maven/gradle 模块管理。

代码不多，但需要有人来做。也可能是你。

### 证明概念

谁不喜欢好的概念证明呢？

你需要花一个上午的时间来弄清楚从 RESTful API 到 GraphQL API 的迁移路径是什么样子的。或者，如果 JSON 响应对象变小了，您可能会尝试测试填满所有可用的本地存储需要多长时间。

这些都是引导解决方案设计下一阶段的问题。在生产中尝试之前，先证明你的想法在小范围内是否可行。

此外，给自己一个 4 小时的自由时间来弄清楚事情是如何运作的，这基本上是一个付费的治疗过程。细细品味。

### 重构

技术债务不断累积。它发生了。

你刚好在会议间隙有一个小时的空闲时间？为什么不考虑将一些常见的功能提取到一个[策略](https://sourcemaking.com/design_patterns/strategy)中。也许就拿这两个最新的合并请求来说，看看是否有一种方法可以定义一个默认的接口，而不是两个过于相似的实现。

我喜欢至少每隔一周左右找一个团队成员讨论一下想法，看看我们是否能想出一个方法来改进我们的代码库。我们知道我们的行为完全被单元测试和验收测试所覆盖，所以我们重构现有的实现并没有什么坏处。

请记住，没有这种信心，重构就不会发生。

我目前正处于个人目标的中期，将我们的验收测试套件从使用 selenium 远程控制更新到更新的 web 驱动程序实现。我花了近 3 个月的时间一点一点地完成，但我们正在接近目标。

### 配对编程

现在这个是有争议的。不是说[结对编程](https://www.agilealliance.org/glossary/pairing/)，而是你是否应该在这种情况下编程。

老实说，对我来说，这是与初级开发人员的一次重大斗争。当有人向我求助时，我必须克制自己不要跳到驾驶座上。我想编码，我知道答案并且能比他们做得更快…

但这对我们双方都没有好处。我已经知道怎么做了。他们可能已经知道我知道怎么做了。他们自己还不知道怎么做。

将结对编程会话视为通过他人进行编码的一种方式。充分发展你的软技能，这样你就可以在练习过程中与你的搭档交谈，而无需触摸键盘。

## 总之

你可能有机会自己承担一个故事，但取决于团队的规模，这可能不太可能。你最好按照上面列出的四个策略之一来解决这个编码难题。通过为周末找一个有趣的兼职项目。

如果你要完成 30%的配额，我个人不能说我做到了，你需要付出努力。

你会注意到这些技巧中的每一个都有助于[解放团队](https://teachingtechleads.com/how-to-stop-holding-your-team-back/)。

*   你在他们前面建设，这样他们就可以立即投入运行。
*   您证明了实现将在小范围内工作。
*   您在不影响系统整体行为的情况下提高了代码质量。
*   你实际上是在提高与你搭档的开发人员。

永远记住，你的工作是让团队充满活力。