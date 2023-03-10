# 代码注释是你的代码自传

> 原文：<https://dev.to/dejavo/code-comments-are-your-code-autobiography-3c7n>

[![Photo by [Nick Morrison](https://unsplash.com/@nickmorrison?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)](img/ca4cedd3b712bee1af8b29393a6c7d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AJ147cI8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/0%2ApnowXdHHLnnyjagr)

 *原载于[中](https://medium.com/@dvirsegal/code-comments-are-your-code-autobiography-7202c45b5e37)2019 年<time title="Thursday, July 25, 2019, 10:34:42 AM">7 月 25 日</time>* 

写你的代码是多余的；你只需要代码。我可以把时间投入到单元测试或开发其他功能上，以及其他**借口**为什么写代码注释是浪费时间。这是开发人员的普遍看法。

好吧，你已经投入了所有的精力来培养你的代码的设计，你为什么要评论它，对不对？因此，代码文档要么不存在，要么毫无用处。

> ![unknown tweet media content](img/2e67a84a5d7b20697cd638d5f30c399c.png)![Carter Wickstrom profile image](img/35d28f109c649200d28327ddc9c920fc.png)卡特维克斯特伦[@卡特维克斯特伦](https://dev.to/carterwickstrom)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)90%的代码注释:15:14PM-03 2018 年 7 月[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1014165500056596481)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1014165500056596481)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1014165500056596481)

虽然在某些情况下，编写代码注释可能会适得其反。然而，如果做得好，我们是唯一能够从中受益最多的人。

#### 代码注释为合同

自我记录的代码并不存在。你可以写有意义的变量名、方法、类等等。但是尽管它很好，它也不能让你的代码自动描述它的意思。

> ![I Am Devloper - Vote for Pedro profile image](img/12f6bca50e187550f3937f16c1219ece.png)我是开发者——投票给佩德罗@ iamdevloper![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)“我的代码是自我记录的”===“我不擅长团队工作”2019 年 2 月 13 日下午 16:28[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1095721294216089600)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1095721294216089600)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1095721294216089600)

是的，当然在实施过程中，当你把它变得完美时，你是在它的环境中，但是一个月后会发生什么呢？你会记得多少？你能相信未来的自己在接近它时会有同样的心态吗？

意识到你写这段代码时的想法会对你今后的工作有所帮助。正确的代码文档是与维护者签订合同，维护者可能就是你。

#### 代码注释是干净代码的固有部分

在某些情况下，代码注释可以指出代码的味道。如果在编写方法的代码文档时，您发现自己在编写方法做这个做那个。您可能应该重构这个方法，因为它必须做一件事情，而且只能做一件事情。因此，将代码分成两个方法，并立即修改代码注释以适应变化。这样，你就坚持了一个清晰的代码概念。

此外，深入研究代码以更好地理解方法的上下文是不必要的时间浪费，而您可以轻松地阅读方法头并快速回忆起方法的 *what (_obvious)和 _why* 部分，特别是关注*如何*完成它被编写的目的。_

以下面要点中的注释为例，它只是陈述了显而易见的内容，并说明了它的作用——基本上，它重复了属性的名称，这使它变得多余。而删除它将与一开始不写它具有相同的效果: