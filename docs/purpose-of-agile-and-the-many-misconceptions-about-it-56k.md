# 敏捷的目的以及对它的许多误解

> 原文：<https://dev.to/sarafian/purpose-of-agile-and-the-many-misconceptions-about-it-56k>

# 简介

自从敏捷在 it 界流行开来已经有很多年了，大多数组织已经采用了 IT。现实是，理解往往是有限的，期望是错误的，失望来自大。我写这篇文章是因为被大肆宣传的敏捷的继任者是 DEVOPS，同样的事情也在发生。

它始于这样一个事实:敏捷已经被商业化，并通过培训、培训师、工具和认证的方式卖给了组织。公司成立的唯一目标就是销售敏捷，而且它并不便宜。随着时间的流逝，接下来的步骤是扩展敏捷，因为我们都在一个团队中掌握了敏捷。如果你问我，我会说是大生意。为了说服一个组织证明这些花费是合理的，一点小小的调整和扭曲恰好能够推销通常的承诺:以更低的成本做事情。这就是一切开始出错的地方。人们投入大量资金，用一种甚至不是目标成本的工艺来降低他们的工艺成本。

声明一下，我参加过两次敏捷的官方培训，一次是作为 Scrum 联盟的 Scrum Master 参加的，另一次是参加 T2 的敏捷框架(SAFe)培训。他们肯定有他们的用处，但我不能完全证明他们的成本，特别是安全是多么昂贵，因为它延伸到整个组织。

# 你应该对敏捷有什么期待？

如果你问我，从商业的角度来看敏捷的目的是增加可预测性和透明度。如果在这个过程中降低了成本，那就是额外收获。事实上，这需要花费更多的钱来执行，但是它可以通过避免延迟和违背承诺来节省资金。这完全是为了获得更好的结果而移动费用和利润。如果结果是你的商业目标，那么成本是合理的。因此，它是否更便宜，这真的取决于每个案例，但最重要的是，它不是主要的驱动因素。如果只计算执行成本，那么最好远离敏捷，因为它会让你失望，或者你会让别人失望。

# 它是如何工作的？

我不会进入技术措辞，我会简单地为每个人解释这一点，不管背景如何。我也不打算深究 SCRUM 或看板方法，因为这不相关。讲授敏捷不是我这篇文章的目标。毕竟，关于这个话题已经说了很多了。我在这里的目标是解释基本原则，其中许多是在常识和现实生活中发现的，以帮助你理解这些基本原则。

假设一个企业想要实现一个特性。经过一些分析后，团队将工作分解成**个故事**，也称为**待办事项**。将一个故事视为一个原子工作单元，具有清晰的目标、明确的验证和合理的几天内完成的规模。作为分解的一部分，团队讨论故事，找出风险所在，并达成一致。这个过程也被称为**梳理**，虽然你应该知道这个术语已经从官方的字典中删除了，但是大多数人仍然使用它。修饰的最后一部分，是对故事的评估，也称为**讲故事**。团队中的每个人都根据他/她对所需工作、风险、复杂性等的看法进行投票。唯一的要求是他/她的估计是相对于另一个故事的所有人都同意的估计。当所有投票都相似时，则选择出现率最高的投票作为评估，也称为**故事点**。backlog 实际上讲述了团队如何实现特性的故事，以及什么构成了成功的交付。由于软件工程行业的不确定性和快速发展，团队不会对未来进行长期评估。事实证明这是徒劳的。尽管如此，在估计的时间线内，总的故事点反映了对工作量的估计，也就是对所需时间的估计。在使用 SCRUM 时，每个团队都在一小段时间内工作，也称为**冲刺**对于每个 sprint，团队承诺完全实现一些故事。通常，一个人，也称为**产品负责人**，会根据他们的评估和团队的可用性来决定包含哪些故事。一旦对承诺达成共识，冲刺就开始了。短跑通常持续一到三周，大多数持续两周。几周后，团队通过演示的方式向涉众展示他们的成果。这个仪式实际上叫做**演示**。团队有机会展示他们的进展，提高认识，利益相关者也有机会提问，如有必要，修改他们的计划。这是软件工程中非常典型的事情，灵活性非常重要。毕竟，团队可能走向了一个完全错误的方向，但尽早发现这一点并有机会纠正总比在交付前一天发现要好。这也被称为**过早失败**。在演示之后，团队进行回顾，也称为 **Retro** ，他们开始讨论事情变好或变坏，最终目标是为下一次迭代进行改进。在几次冲刺之后，团队计算出他们的速度，也就是每次冲刺消耗多少故事点。

# 这怎么预测？

故事点都是任意的数字，以保护团队将努力和复杂性与时间和义务联系起来。做数学并把两者联系起来并不需要火箭科学，但是以我的经验，它帮助工程师做更好的估计。他们有时被高估，有时被低估。这没多大关系，因为随着时间的推移，当团队学会如何更好地组织和评估时，评估会趋于平均。为了实现这一点并最小化错误的估计，故事通常代表少量的工作，因此要求小故事具有合理的大小。如果故事太大或太复杂，那么估计可能是错误的，因为大的方差对平均值有更大的影响。敏捷的目标是让代表业务的人能够预测某个功能何时完成，或者选择哪些部分可以在给定的期限内成功实现。这是通过将总估算值除以冲刺速度来计算需要多少次冲刺来实现的。这只是一个估计，但比请人估计复杂的事情需要多长时间要可信得多。这就是可预测性是如何实现的。

# 所有这些会议呢？

注意，在这个过程中有一些开销，许多人不同意。特别是在开始的时候，团队似乎陷入了官僚主义的狂热，积压的工作得到了管理，太多的会议召开了。感觉违反直觉。看起来项目管理也是团队的职责，这在一定程度上是正确的，因为敏捷的一部分就是允许团队自我组织。随着时间的推移，尊重和自我完善的承诺，团队找到了自己的方式，这成为工作的一部分。随着时间的推移，团队会发现流程的哪些部分是隐含的，他们倾向于最小化开销。无论如何，你应该预料到实现的成本会上升，尤其是在第一阶段。

# 试玩——我的圣杯

不要急于谈论它如何帮助工程师，有两个过程对每个人的发展都至关重要。在我看来，演示是进行敏捷时最重要的仪式。它需要团队的准备，应该总是由不同的人来执行。示范人永远不会出现。除了在透明度和交流方面的有用性，它还帮助人们学习如何向不是专家的听众解释一个主题。它还帮助非专业人士理解开发过程中面临的障碍。总的来说，人们学习如何站在舞台上。这是许多技术档案中缺少的东西，这些技术档案在历史上会避开聚光灯。这可能是一段艰难的旅程，需要时间去掌握。作为学习过程的一部分，还需要经历几次失败。能够解释某事是良好知识的最重要标志之一。演示使人们更有见识，对他们的职业发展有全面的帮助。

# 缩放敏捷

敏捷如何为不在团队中的人服务？这确实是一个挑战，但或多或少都遵循类似的过程。对多项功能进行了高层次的讨论，并提供了评估。此时，估计值出错的几率非常高，但就像低层次的故事一样，它们往往会趋于平均。团队通常由产品经理和架构师组成，他们都试图找出一个高层次的特性是如何融入拼图中的。这不仅仅是工作和复杂性评估，还包括团队可用性、未知主题、培训等。有一些关于如何选择下一步要实现的特性的最佳实践，但是归根结底，这是参与讨论和业务分析的人的经验问题。

# 哪里出问题了？

这一切都始于降低成本的期望。敏捷是一种文化，因此，它永远无法解决一个组织的固有问题。事实上，如果本组织不愿意适应，它将扩大问题并制造更多的问题。敏捷是关于文化和工作方式的。目标很简单，手段更少结构化和更少控制。虽然它旨在帮助业务，但它的重点是团队，并使团队最具生产力。因此，只有在驱动因素与技术和效率相关的组织中，敏捷才能发挥作用。它还要求团队的一致性和高度一致性，很少与来自不同雇主、不同地点和不同参与类型的成员合作，因为他们的激励机制不同。因此，当敏捷被企业作为某种项目管理来运行时，它通常会出错。有太多的干扰和太多的决定使这个过程无法进行。总有一个好的借口，但总的来说，要避免导致团队效率低下的常见错误，需要很多商业纪律和大量学习。这样的团队，没有任何成果，成本很高。

通常，在这一点上，敏捷会受到指责，因为它没有实现最初的卖点，这也不是敏捷的错。根本原因还是文化，这种文化认为买东西，插上电源，然后*瞧，事情发生得更快*。你不买敏捷，你想出办法，你就变得敏捷了。因此，敏捷失败的最典型的原因是当业务和技术不一致，没有用相同的目标思考时。这一点已经得到了认可，并导致了 DEVOPS 文化的引入，用最简单的术语来说，业务和技术之间没有界限。随着技术在当今社会占据主导地位，这意味着技术和传统商业一样成为商业。这需要一个组织的文化和心态发生重大变化。这是组织为了在这个新的快节奏的技术时代中生存而必须面对的最具挑战性的任务。你可以在我的文章[中读到更多关于 DevOps](https://sarafian.github.io/culture/2018/09/14/devops-vs-automation-culture-what-is.html) 的概要。

我并不是说敏捷公司和咨询公司是错误的。但是，需要理解的是，当他们只能帮助引导你进入敏捷，然后你把事情搞清楚的时候，他们最终会把敏捷当成商品来卖。我见过这样的顾问进入组织只是为了推销培训、认证，通常还有他们自己的工具。

# 适应症敏捷不佳

在大家都在做敏捷的情况下，怎么会有人认识到敏捷的误区和失败？有什么指征？

*   当成本经常成为反对敏捷的理由时。
*   当演示仪式还没结束时。
*   当代表业务的产品所有者与其他工程师不在同一个组织结构中时。
*   当流程中有太多不同的角色时。
*   当过程中有太多不相关的人。
*   当*修正敏捷*被认为是你挥舞的可以解决一切的魔杖时。
*   当你经常听到美国的 T1 和 T2 的 T3 时。
*   当一切都与过程有关，而过程接管了一切。
*   当某些停机或问题以类似模式频繁发生时。
*   停机或出现问题后，没有事后分析。
*   当一切都在电子邮件或类似的东西中或在人们的头脑中。
*   当项目管理占主导地位并且非常接近团队时。
*   当项目驱动团队组成和能力时。
*   当工具和流程比人更重要的时候。
*   当 scrum master 是项目经理时。
*   当敏捷经常被提及时。听起来很奇怪，敏捷是隐含的。反复提到敏捷，说明你不在。

我认为 Scrum 大师的角色是反敏捷的。这个角色对于在敏捷中引入一个团队是非常必要的，但是与通常的实践相反，我认为一旦团队开始走自己的路，这个角色就变得过时了。如果有一个 scrum 大师，那么他应该是负责所有团队的人。我之所以得出这个结论，是因为我曾与非常高效的团队合作过，实际上所有需要做的事情都是由团队的任何成员来处理的。与此同时，所有由于组织中的文化而无法解决的问题，scrum master 也无法解决，这实际上否定了角色的有用性。在一天结束的时候，如果一个团队多次提到某件事情出错了，或者某件事情出错了几次，那么不管有没有 scrum master，倾听并决定(或不)提供一个解决方案只是组织中的文化问题。没有 100%的 scrum master 角色的位置。这只存在于书籍和卖家中。我的推理反映在一个简单的问题上，即 scrum master 是否是技术型的:

*   如果不是这样，通常情况下，他/她通常会干扰团队。因为这个角色很少证明全职参与是正确的，所以 scrum master 角色经常与项目管理职责混合在一起。一个好的项目经理是一个极其重要的角色，但是在敏捷团队中却是一个非常糟糕的角色。
*   如果是，那么团队中的任何人都可以计划一些会议，为什么还要浪费他/她的技能呢？