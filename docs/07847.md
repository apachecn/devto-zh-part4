# KCDC 2019 和代码审查最佳实践

> 原文：<https://dev.to/gabidombrowski/kcdc-2019-and-best-practices-for-code-reviews-4jll>

7 月，我有机会作为 [Crema](https://www.crema.us/) 的员工第一次参加 [KCDC 2019](https://www.kcdc.info/) 。我得说 1)我很兴奋，2)我很兴奋是对的。能够成为当地发展社区的一员是一件很棒的事情。这是一个支持性的社区，有很棒的人保持它的易接近性和包容性。

会议本身易于操作，组织良好。这是一个技术不可知的会议，每个人都有自己的东西。在几个时间段里，很难挑选一个单独的演讲来参加！会议还精心安排了大量高质量的人类技能讲座。

我发现最相关的人类技能谈话是*“如何进行开发人员真正想要的代码评审”*。演讲者[卡梅隆·普莱斯利](http://blog.thesoftwarementor.com)，以一种实用而吸引人的方式讲述了内容。他的演讲重申了代码评审的目标是评估质量、可读性和可维护性的观点。代码评审也应该加强风格的一致性。请记住，期望的结果是解决手头的问题，而不是让每个人的代码看起来都一样。区分固执己见的感叹词和可教的，适用的时刻。

演讲概述了代码评审**总是**应该做以下事情:

> 他们应该尊重。请记住，在评论时，有人努力工作的代码。
> 
> 它们应该加强共同所有权。继续说"我们"在你们身上。

[![Dog floating in zero G with the caption "weeeeeeeeeee!"](img/7771f93a878c68a88b53b88bc828af43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ng8AtQ3m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5sa4twy5d08bx6lllpkd.gif)

代码评审是确保代码质量的一个重要部分，我们都要对质量负责，无论职位高低。代码审查也有很大的潜力成为指导时刻。你不仅仅是在预防问题，你也在帮助开发人员提高他们的技能。所以，当你提供评论的时候，提供上下文。比如说，*“当 X 发生时，我们应该做 Y，因为 z”.*

我之前没有想到的一点是，你不必只在代码完成时才进行代码审查。我倾向于不希望任何人在我的代码漂亮而闪亮之前看到它，因为，嗯， [#impostersyndrome](https://twitter.com/search?q=%23impostersyndrome) 。实际上，在编写代码的时候共享代码可以在编写的过程中改进代码，而不是要求你在最后一次进行大规模的大脑上传。你也可以@某个具体的人，指出你希望他们提供反馈的确切内容。

[![The Matrix - "I know Kung Fu"](img/a3ce493babf0a2cd92fc6aa53b675649.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cXuhbVdQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/64oyg3stkb86rzfpkujd.gif)

其他一些有用的准则:

*   一次不要超过 60 分钟的代码审查。
*   试着将评论保持在 400 行代码以下——超过 400 行就很难概念化了。
*   试着花大约 30%的时间在代码审查上。

感谢 KCDC 的组织者、演讲者和所有参与社区的人，感谢他们提供了一个难以置信的空间来学习和了解我的开发伙伴们！

2020 年 KCDC 再见！