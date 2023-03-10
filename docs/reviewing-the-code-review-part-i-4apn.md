# 审查代码审查，第一部分

> 原文：<https://dev.to/codestream/reviewing-the-code-review-part-i-4apn>

我们的一些客户告诉我们，他们正在使用 CodeStream 来缩短多达 50%的代码审查。这是一种轻量级的方法，我们正试图从他们的经验和反馈中学习，并改进我们提供的过程和功能，以使那些代码审查更好。CodeStream 引入了持续代码审查的概念，这是一个轻量级的编辑器内过程，可以随时审查代码库的任何部分。使代码评审持续意味着您可以在过程的早期获得反馈，例如在您剔除函数和类时，在您构建新功能的繁重开发过程中，在您从您的团队寻求反馈时，在合并之前，重要的是，即使对于前一段时间合并的代码，当您在代码库中遇到技术债务时。

### 代码评审怎么了？

根据 Traci Lum 在她的文章[如何给出和获得更好的代码评审](https://hackernoon.com/how-to-give-and-get-better-code-reviews-e011c3cda55e)中所说，代码评审是开发人员最**害怕的**活动之一。开发人员通常花很多时间去创造、想象和发明(通常是靠他们自己，不受干扰)，他们面临着没有创造性的程序性判断。她写道...这是你，开发者，工匠，艺术家，在评论家委员会面前展示你的作品，并乞求他们用竖起大拇指的表情符号和“LGTM”来祝福你的杰作的时候。"".

当然，代码评审对于维护代码质量是必要的。为什么如此恐惧？怎么做才能显著降低？不同的方法有什么好处？代码审查的大部分问题可以归结为三个要素:

1.  在编写代码和审查代码之间经过的时间，
2.  由该进程强制执行的上下文切换，从代码切换到浏览器，或者切换到 ide 外部的不同上下文，最后
3.  事实上，典型的审查只查看任何给定的代码一次——它正在准备部署——并且没有考虑持续的改进过程。

在这种背景下，让我们讨论一下 Traci 提供的关于如何改进代码评审过程的一些建议，并对改进提出*改进。*

开发人员通常生活在他们的 IDE 中。那是写代码、修改、比较等的地方。不在你的 IDE 中执行代码评审的原因是**时机**的偶然。如果 ide 能够更早地扩展，我们将会看到在上下文中执行代码审查的所有好处，并且这个过程的脱节本质可能会被避免。

### 你的 IDE 是讨论代码的最佳场所

Traci Lum 的建议是有帮助的和合理的，它们集中在改善 PR 过程上。让我们总结一下她的建议，同时指出如果代码评审早一点开始，并且起源于您的 IDE 而不是 PR，可能会产生的潜在改进。

如果你想让**得到**更好的代码评测，她的建议包括:

*   从公共关系的主体链接到 GitHub 问题或 JIRA 票
    *   如果没有 PR，您可以在 IDE 中为代码本身生成或附加吉拉票证或 GitHub 问题，指派某人进行修复或审查，并使该链接永久存在，并对阅读代码的任何队友可见，那会怎么样？
*   写一份变化、权衡和剩余待办事项的快速总结或列表
    *   如果变更摘要只是在您的 IDE 中自动生成的报告，包括所有的问题和顾虑，会怎么样？
*   用标签标记拉请求，并在正式提交审查之前审查您自己的代码
    *   如果您可以对任何代码块进行标记和注释，并使您团队中的所有开发人员都能在编辑器中轻松访问这些代码块，会怎么样？
*   在你特别不确定或需要反馈的地方做注释
    *   如果您可以在编写代码时随时这样做，甚至在 PR 之前，这样反馈可以帮助您避免死胡同，从而减少错误和技术债务，那会怎么样？
*   标记正确的人
    *   如果一旦你在 IDE 中选择了任何代码块，就会有一个即时的 git 责任查找，让你知道是谁编写或编辑了该代码，并且那个人会自动出现在你的组合框中。
*   深呼吸，把评论内化。
    *   *总是好建议。如果整个对话被保存下来，并且可以被双方(最终是团队中的任何一个开发人员)作为解释决策是如何做出的有用文档来访问，会怎么样呢？*

如果你想让**给**更好的代码评审，她的建议是:

*   提问而不是表态，就是 wtf 少
    *   如果您可以分享一个代码示例来说明一个解决方案，并且它自动链接到评审，会怎么样？
*   阐明问题(如果有的话)并提出替代方案
*   尝试理解上下文和建议的解决方案
*   重点赢得*(她的意思是，给出反馈时要积极)*

如果 PR 仍然是进行代码评审的主要地方，Traci 建议的所有步骤都是可以的。然而，如果我们准备考虑更早地开始审查过程，在 PR 之前，不受 PR 的阻碍，代码审查变得连续，也就是说，在任何时间，对任何代码行，对任何问题。

在第 2 部分中，我们将探索改进代码审查的其他建议，并提供如何使事情变得更简单、更高效和更愉快的想法。