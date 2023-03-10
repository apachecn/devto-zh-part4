# 关于 Layer API 关闭的思考

> 原文：<https://dev.to/tschellenbach/thoughts-on-layer-s-api-shutdown-1fan>

[![Layer Chat](img/53860128f9a67c7bdb46a2ff82e86c30.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--88xOqy3s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://getstream-blog.imgix.net/blog/wp-content/uploads/2019/07/image1.jpg%3Fw%3D1024%26h%3D1024)

在过去的几天里，我们一直愉快地将[不满的](https://getstream.io/blog/layer-shutting-down-all-chat-operations/)层[客户](https://layer.com/)转移到[流](https://getstream.io/chat/)。10 月 30 日，他们宣布关闭 API，这对我们的增长来说是相当惊人的。

然而，作为一名开发者，看到一家公司被收购，然后在这么短的时间内关闭他们的产品，这让我很恼火。所有这些客户都信任一个产品、一个团队和一个愿景。关闭它，尤其是在收购后这么短的时间内，看起来感觉像是对那些客户的背叛。我个人认为，通过收购一家公司和客户群，你有道德义务尽一切努力继续为这些客户服务，或者至少为他们提供一个不错的退出策略。

以下是 Layer 的完整电子邮件:

> 我们正在日落层平台
> 
> 我们将在 2019 年 10 月 30 日日落层平台和 API。
> 
> 我们服务的平台、特性和功能将保持不变，包括导出数据的权限。
> 
> 2019 年 10 月 30 日后，Layer 平台及所有与 Layer 相关的服务将全部关闭。
> 
> 我们的首要任务是维持系统正常运行时间，并确保您能够尽可能无缝地离开平台。我们的 SDK 不会发布任何功能或更新，与服务正常运行时间或核心功能无关的错误可能不会得到解决。
> 
> 过了这个日期，Layer 将不再在线，也没有继续利用 Layer 后端服务的选项。同样，也没有办法把层后端服务和自己的主机或我们提供给你。
> 
> 为什么我们要淘汰层平台？
> 
> 1)我们没有合适的团队来正确诊断和解决客户群中出现的大量 bug 和问题。
> 
> 2)我们没有一个团队来保持我们的 SDK 是最新的，事实是我们无法解决的关键错误和失败是不可避免的，或早或晚。
> 
> 3)虽然层平台和组织中内置的安全性非常持久，但我们没有适当的团队来充分协助您完成供应商审核或提供所需的验证。
> 
> 4)我们负责所有服务器运营和基础架构的关键员工最近突然意外去世。虽然我们计划在图层服务的剩余部分获得额外的帮助，但我们没有可持续的途径来确保系统正常运行时间和长期可靠性。
> 
> 5)除了上述所有情况，层基础架构的成本非常高，而且非常固定。我们目前正在以不可持续的亏损运营 Layer 平台。
> 
> 结束一切
> 
> 在商业方面，我们肯定搞砸了，并在这个过程中学到了很多。我知道这可能会让你处于非常艰难的境地，我很抱歉我没有更好的消息与你分享。
> 
> 我也知道时间很紧，但这是为了我们双方的安全。我不希望发生的是，我说我们可以继续支持您 6 个月，然后从现在起 4 个月，我们有一个关键的中断，我们无法与我们的团队解决。那会让我们两个都处于更糟糕的境地。
> 
> 如果我可以帮助推荐一项有竞争力的服务，请告诉我与您最相关的使用案例。
> 
> 谢谢你，
> 
> 肖恩·范威尔登
> 层负责人

从一个聊天提供商迁移到另一个聊天提供商相当麻烦。Stream 试图尽可能地简化这一过程，并提供了图层数据的全自动导入。看看这个 Github Repo，它解释了如何[导出你的层数据并迁移到流](https://github.com/GetStream/layer-migration)。所以，理论上，你可以在几天内迁移。不过，三个月感觉时间太短了。开发团队可能正忙于其他项目，也许他们甚至没有积极地开发这些应用程序。

当 Parse 关闭时，至少有一个到竞争服务的清晰的迁移路径。他们还提供了一个开源解决方案。Layer 不这么做是有道理的，因为他们的核心产品似乎有很多性能、成本和稳定性问题。尽管如此，移植到其他服务的规定路径将是理想的。

从外部来看，你想知道层的关闭是否是由追逐超增长引起的。Layer 筹集了 2950 万美元，并开始向开发者和产品所有者销售他们的产品。一旦他们发现他们无法创造风投们所期待的超高速增长，他们就转向了更加客户支持/营销的垂直市场。也许他们的基础设施的许多技术债务和成本问题可以通过稍微不那么激进的增长战略来避免。

这次关闭让我想起了 Parse 的故事，或者 Keen 和 Travis 的收购，其中大部分/所有的开发团队都被替换了。

这里有一个来自小溪的誓言，只要我能做点什么我们就不会:

1.  如果疯狂的增长目标没有实现，筹集过多的资金会使公司的可持续性处于危险之中
2.  卖给那些会关闭产品或剥离开发团队的公司

现在，我不想对这些公司过于苛刻。有时候你就是没有什么好的选择。尤其是在这种情况下，Layer 的一名团队成员不幸去世。总有你预料不到的事件。考虑到这一点，我和我的联合创始人托马索将尽最大努力关注可持续发展，确保 API 持续可用。