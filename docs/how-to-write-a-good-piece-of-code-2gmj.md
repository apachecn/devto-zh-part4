# 如何写好一段代码

> 原文：<https://dev.to/taillogs/how-to-write-a-good-piece-of-code-2gmj>

[来源](https://steemit.com/technology/@trollfarmer/the-only-valid-measurement-of-code-quality)

# 如何写好一段代码

[![](img/cb4a408ef27c9d6021e9e8813a0bfd57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AGIt61Vy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23bgglkzrdb3x8bghsq7.png) 
[XKCD](https://xkcd.com/844/)

## 确保你的代码“能好”

编写一段好代码的第一步，也可能是最重要的一步，就是根本不要编码。

*   你验证了你的假设了吗？
*   守则的范围是什么？
*   它将如何影响现有的代码？
*   有人已经写好这段代码了吗？

能够回答这样的问题是一段好代码的基础。

#### 与他人讨论

验证你的选择的最好方法是获得他人的意见。努力处在一个人们不怕挑战你的决定和理想的环境中。

从正确的角度看，即使是最坚固的墙也会显得脆弱。

[![](img/e42efd806fa75c06d3e3d7c740360c86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3lBml6Ks--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dqqy5frcjg54zb5l57nr.gif)

## 分解一下

既然你确信你的代码“可以很好”，那么是时候弄清楚如何让它变得更好了。首先，从 API 的角度考虑，尝试将你的代码分解成尽可能小的部分。

理解如何将任务分解成更小的部分，是我见过的初级程序最头疼的事情。记住，你分解的代码块是别人能够帮助你的。就像一块巨石，它只会让你与团队隔绝。

代码设计阶段的第一部分应该很少涉及实现。相反，你应该处理需求和约束。花在实现上的时间通常是浪费时间，因为高级别的 API 更改会使实现假设无效。以我个人的经验，用已经达成一致的 API 开始实现讨论，通常会使讨论进行得更顺利。

## 在写测试之前先写测试定义它(*辛辣而固执己见*)

[![maxwidth](img/c6d253e13d7cf53f188f3785fe2760bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XeTgD8YP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ax7EOLLiBen5tA62cvjF6Pw.gif)

现在你知道如何破解代码了。为你识别的每个独立单元写一个测试。在你编码之前，为你的代码将要暴露的每一部分功能编写一个测试，是 TDD(测试驱动开发)的定义特征。已经有很多关于 TDD 有效性的研究。虽然有些研究有争议，但几乎所有的研究都报告了使用 TDD 后 bug 数量的积极改善。

编辑:我最初声称 TDD 减少了 40%-80%的 bug。在收到这个 Reddit 帖子中的评论后，我意识到这是一个带有固有偏见的数据表示。相反，我在下面附上了一张研究结果的图片，所以你可以自己判断。我还加入了作者的序言段落。

> 结果有时是有争议的(在学术研究中更是如此)。考虑到无与伦比的测量方法以及将 TDD 的影响从许多其他环境变量中分离出来的困难，这并不奇怪。此外，许多研究没有统计能力来进行归纳。因此，我们建议读者在每项研究的背景和环境中考虑经验发现。

[![](img/d1b8d01986ce26c345c168756d06fd0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---CdtZfmx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://csdl-images.computer.org/mags/so/2007/03/figures/s3024t1.gif)

> 2005 年的一项研究发现，使用 TDD 意味着编写更多的测试，反过来，编写更多测试的程序员往往更有效率。关于代码质量和 TDD 与生产率之间更直接的关系的假设是不确定的。关于代码质量和 TDD 与生产率之间更直接的关系的假设是不确定的。

[来源:维基百科](https://en.wikipedia.org/wiki/Test-driven_development#Benefits)

我相信测试驱动开发迫使你首先把自己放在用户的角度，这将导致一组更实用和自然的 API。

抵制同时处理多项任务的诱惑。您应该为代码的单个单元编写失败的测试，然后为该测试编写实现。这将允许您有效地验证您的设计，并维护测试覆盖率，即使您正在向代码库添加代码。

## 保持你的代码一致

个人风格和偏好会因开发者而异。不应该不同的是代码的一致性。对于变量和声明，应该有一致且可预测的命名约定。如果你使用制表符，你应该在任何地方都使用制表符。如果你使用空格，你应该在任何地方使用空格。

许多初级开发人员被每个选择的细微差别所困扰。事实上，更重要的是你对自己的选择有多信任。乍一看，这似乎是一个相对较小的任务，但是一致性远远超出了制表符和空格。

代码的逻辑也需要保持一致。为什么这里用一个`map`，那里用一个`for each`？为什么有些地方用`var`，有些地方用`let`和`const`？可预测性是程序员(或一般人)最难找到的特征之一，也是最有价值的特征之一。

作为一名程序员，你的价值由你的“最大潜在价值”乘以你的“预计风险”来定义。没有可靠性，质量是没有意义的。

## 回顾一下

[![](img/3e5af7d3996d09e7b29ca1ffc7977207.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dDPvThzd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/88nbbnrto36gb7bychuk.png) 
[来源](http://geek-and-poke.com/geekandpoke/2010/11/1/how-to-make-a-good-code-review.html)

如果代码进入 master，它应该被审查。要使一篇评论有益，作者需要真正理解评论过程的价值。

这辈子你不会知道所有的事情。

一个好的程序员写出伟大的代码，却不会让它被审查。

一个伟大的程序员写出了体面的代码，但却要经过严格的审查过程。

你应该考虑生活中各个方面的失败，包括编码。错误是会犯的，通常需要另一双眼睛来阻止它们。

## 发货吧

恭喜你，你现在已经写了一段很好的代码。没有这个过程，可能写出一段好的代码，但是没有这个过程，不可能“总是写出一段好的代码”。

发货后，记得与你的团队交流你的成果，这可能会解除某人的障碍。

## 别想多了

[![](img/67fb1f3568ffef813652a71504ee68ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8QOtke_3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b2jkkomgbd7za47ovbb2.png)

这里的每一条规则都应该有所保留。一个 2 行提交的内部`README`真的应该被审查吗？

争取最佳实践，但保持务实和理性，不要设计那些一开始就不需要设计的东西。你的武器库中最重要的工具是你的直觉。规则的存在不是为了挡你的路，它们的存在是为了当你不是(你也不会是)时保持一致和可靠。

[![Foo](img/ad9b9da1674b387c7b673d8f5867bfc0.png)](https://twitter.com/taillogs)

[我的博客](https://www.cdevn.com/how-to-write-a-good-piece-of-code)