# 如何执行代码审查？

> 原文：<https://dev.to/rlxdprogrammer/how-to-perform-a-code-review-4m3j>

介绍

在大多数公司和项目中，有一种叫做四眼原则的东西。这意味着没有人被允许单独做出改变，至少还需要一个人来批准这些改变。四眼原则的一个流行实现是进行代码审查。作为替代，你可以结对编程。但是什么是代码审查呢？如何高效地执行？

# 代码评审的目标是什么？

代码评审是实现代码的一个质量关口。目标是其他(通常更有经验的)同事检查代码变更，提出建议(所谓的发现)并决定代码是否可以合并，或者仍然需要一些变更？

代码审查的结果需要记录在案。

代码评审的目标是实现更好的代码质量，并在团队内部分享经验。

# 在代码评审中要检查什么？

代码审查的第一个目标是检查代码中的潜在错误:检查代码是否做了应该做的事情，检查错误，如使用未初始化的变量、无休止的循环、不正确的内存处理、死代码等。

此外，大多数项目应该遵循预定义的编码指南。这个指导方针取决于所使用的编程语言，它描述了代码应该是什么样子:需要避免哪些错误的概念，顺序或声明应该是什么，如何命名你的文件，哪些语言元素可以或不可以使用，如何命名你的实体(函数、类、变量等)。)，如何组织你的代码等等。

在代码审查期间，还应该检查是否遵循了该指南。

最后，还可以为代码开发人员提出一些建议，如何以更好的方式完成这项工作。

重要的是，这些点中有几个可以通过自动化代码分析工具(死代码、命名约定等)来检查。)，使用这样的工具总是一个很好的做法。

有一个通用的检查表来说明在评审过程中应该检查什么也是一个很好的做法。以便审阅者可以逐点检查该清单，以确保没有遗漏任何内容。

区分主要发现和次要发现也是一种很好的做法。主要的发现是必须改变的，次要的发现只是“必须改变就好了”。

# 执行代码评审的可能方式

有两种主要的复习方式:在线复习(面对面)或离线复习。网络版花费的时间更多，但也有一些好处。

## 在线评论

在线执行代码评审意味着代码作者与一个或多个其他开发人员坐在一起。作者展示了他实现的代码，并描述了它们的实现细节和设计决策。其他开发人员可以随时提出问题和建议。

应该创建一个协议，包含评审会议的每一点。

这种方法的最大优点是，评审者可以更好、更深入地理解实现，决策可以以快速的方式阐明，代码的作者可以获得详细、直接的反馈。

如果发现太多，一旦发现确定后，需要重复召开审查会议。

## 离线回顾

对于离线审查，最好使用某种在线工具。在这种情况下，一个或多个开发人员在他们自己的计算机上离线审查代码变更，并将他们的发现和问题添加到工具中。然后，代码的作者需要回答问题并修正发现的问题。它需要一次又一次地重复，直到审阅者不批准这些更改。这种方法的优点是评审人员和开发人员不需要在同一个地方，他们可以在不同的时间进行评审，不同的评审人员可以相互独立地进行评审。缺点是审查者不会深入细节，在很多问题的情况下会花费很长时间，并且代码的作者不会得到详细的(口头的)反馈。

# 谁应该审核代码？

总是取决于项目。在某些项目中，任何开发人员都可以执行代码审查(通常在更敏捷的团队中)。如果团队成员的知识水平相似，通常就是这种情况。

在其他情况下，审查必须由一些领域专家、技术专家或软件架构师来执行。多位审稿人总是受欢迎的。这通常在有许多不同专家角色的结构中完成。

# 总结

代码评审是重要的质量关口。执行评审的最佳方式总是取决于项目的需求。但无论如何，它应该有一个清晰的、记录在案的过程和记录在案的结果。否则，执行代码审查可能只是浪费时间。