# 坦白:我是如何得到(我的 SaaS 戈朗作品)多租户的

> 原文：<https://dev.to/golangch/confession-how-i-got-my-piece-of-saas-multi-tenant-2m7p>

交叉发布:[我如何获得多租户](https://blog.altafino.com/software-development/confession-how-i-got-my-piece-of-saas-multi-tenant/)

正如我的一些追随者可能知道的，我正在编写并提供一个[求职板软件](https://www.boardengine.io)，从上周末开始，我在上面运行了 4 个不同的求职板。

和...

### 今天我坦白，我从一开始就没有落实所有细节

我从今年二月底开始运行的[全栈开发人员工作板](https://fullstackjob.com)就是其中之一，使用的是相同的软件。然而，在当时，该软件根本不是多租户的。然而，底层软件必须是多租户的计划一直存在。

不知道，多租户是什么意思，为什么可以好？[好文章](https://www.netsolutions.com/insights/5-reasons-why-you-should-choose-multi-tenant-architecture-for-your-saas-application/)

[![](img/c88e58d45bfd4e834357999141b44e27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RsUycOTP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ule39semlcohzren70z.jpg)

现在...为什么我没有从一开始就做多租户....好问题。在过去的几十年里，我正是这样做的，并且记得一些以前的项目，是的，它们就从那里开始...是的...大部分时间都卡在那里...

### 为什么我会被困在那里？

不，不是因为这不可能，我也办不到...然而，因为做多租户软件会增加软件的复杂性，这一点不应该被低估。此外，当你作为一个制造商做一个新项目时，在你确定你的产品会找到客户之前，你不应该浪费时间先做一些多租户的事情。

这就是我在过去 3 周才开始添加多租户的原因。

好的..3 个星期，只在清晨和周末的时候工作，对这个来说不算什么，但是我必须承认，当我构建我的模型、对象、端点甚至前端服务时，我总是在思考这个问题。不确切地说，我最终将如何实现它，但思考它以后将“插”在哪里。

所以我可以+/-快速地实现它，现在 boardengine.io 软件是一个 SaaS，它可以在相同的实例(简化:1 个数据库，1 个后端，1 个前端代码)上运行不同的作业板，其中样式不同，内容部分不同，数据是隔离的，但也可以在租户之间共享，一些逻辑，如过滤、技能集等可以在实例级别上按租户进行配置。我没有 SSO，但是用户可以在 1-n 租户等上拥有角色。包含后端服务、2 个 http 守护进程、redis、nsq、arangodb 和前端的完整实例运行在 Rancher 1.6 docker 环境中。我有计划(也是从一开始)转移到基于 Kubernetes 的环境，但同样的方式...为搬迁做好准备，但只在真正需要的时候才这样做。

### 我把从自己的忏悔中得到的东西当作学问:

#### 当你开始一个新的软件项目时，要有远大的想法，但要循序渐进

[![](img/6d509ef25fad2ce7bdfac4a609c1a721.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xkbL4RpX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j0o8q22n9ml465qi8dal.png)

*   确定第一次迭代所需的最小架构
*   心中有了“外挂”点，将来才能达到大目标，但是
*   不要一开始就过度工程化。

随着时间的推移，大局可能会发生变化，所以即使定义一个详细的范围也没有多大意义，因为这可能会发生变化。从小处着手，尽早从真实客户那里获得关于你的产品的第一个版本的反馈的一个好办法是，你可以在实现一个复杂层之前*调整大局和相关范围。但是，如上所列。从一开始就考虑全局非常重要，不要关闭体系结构的大门，避免“几乎不可能完成的任务”类型的数据迁移。*

最后是运行在 [boardengine.io 作业板软件](https://www.boardengine.io)上的板列表:

*   [全栈开发者乔布斯:https://fullstackjob.com](https://fullstackjob.com)
*   https://reactjsjob.com
*   [JavaScript Jobs:https://JavaScript job . XYZ](https://javascriptjob.xyz)
*   [Python Jobs:https://Python job . XYZ](https://pythonjob.xyz)
*   [Rust 开发者工作:https://rustjob.xyz](https://pythonjob.xyz)
*   [Golang Jobs:https://Golang job . XYZ](https://golangjob.xyz)

如果你对一个更技术性的帖子感兴趣，我是如何实现的，请评论，就像你有任何其他反馈一样:-)

斯特凡，干杯

[![](img/3050e6720d743acdaa8722f57e160e73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lVQcVPm5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7r9mge38ywafhiwg2mwe.gif)

(对您自己的多租户软件感兴趣-通过我的[全栈开发者](https://www.altafino.com)公司联系我)