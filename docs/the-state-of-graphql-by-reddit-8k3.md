# Reddit 的 GraphQL 的状态

> 原文：<https://dev.to/robmatyszewski/the-state-of-graphql-by-reddit-8k3>

有很多关于 GraphQL 的宣传和争论，将它与 REST 进行比较。GraphQL 在全球范围内还处于被采用的早期阶段，没有人确切知道它会在哪里结束。在互联网上搜索，我能够找到许多积极的文章介绍这项新技术。只是第一印象的炒作吗？

我研究了 Reddit，选择了 GraphQL 上投票最多的评论。我的目标是写下尽可能多的关于这个话题的透明和客观的文章。我利用用户之间的讨论和争论来呈现每个方面的不同观点。下面的每个评论引用者都有一个链接指向其作者和()中的投票数。请记住，自从我写这篇文章以来，upvote 的数字可能会改变。

## 议程

*   一般评论
*   React &阿波罗评论
*   大男孩& GraphQL
*   贮藏
*   数据提取
*   总结。

# 总评

从一般的观点出发，我选择了两种情况。首先，one - [SwiftOneSpeaks](https://www.reddit.com/user/SwiftOneSpeaks/) 展示了前端开发人员的观点和上市时间方面的潜在改进。其次， [Scruffles360](https://www.reddit.com/user/scruffles360/) 展示了团队如何采用 graphql 以及他们使用了哪一种 graph QL 的策略趋势。稍后你会发现更多关于他的情况。第二条评论是我在文章中选择的投票率最低的一条。

[雨燕峰](https://www.reddit.com/r/reactjs/comments/bozrg1/graphql_vs_rest_putting_rest_to_rest/eno3ovb/) (23)说:

> 当我与后端开发团队合作时，他们更愿意提供新的查询来满足我的需求，因为这不会影响他们必须支持的现有查询。(也就是说，我不知道随着时间的推移，这种扩展会有多好)。它还减少了我必须重新解析成可用(满足我的需求)数据结构的蹩脚响应的数量。(例如，我得到 3 个数组，我必须将它们关联起来并压缩成一组对象。有了 GraphQL，我更有能力以一种有用的形式要求数据，尽管后端仍然必须做好自己的工作。

[Scruffles360](https://www.reddit.com/r/reactjs/comments/bozrg1/graphql_vs_rest_putting_rest_to_rest/enpb6fg/) (8)解释了他在 GraphQL 范围内的三个趋势:

> 巨石柱——这就是阿波罗现在正在推动的东西。每个公司都有一个且只有一个 api 端点和模式，代理其他所有东西(【https://principledgraphql.com/】T2)。我完全不同意这一点，但不会在这里重复我的论点(如果你愿意，可以翻翻我的评论历史)*   Database API-For some strange reason, people have started to add plug-ins to the database to let you directly access the database through graphql. Graphql is great in many ways, but it can't compete with the native database query language. More importantly, this removes your business layer and allows callers to visit your store directly. No one can enter your store except a micro-service. Others should call through your api.*   Intermediate method-classic API method, each application has its own API (graphql in this example). It may isolate business logic or agents to microservices (through rest or splicing another Graphql schema through schema). That's the road we've traveled, and I don't regret it.T9】

# 反应过来&阿波罗回顾

React & Apollo 组合评审请求获得了很多关注。此外[错误地佐罗](https://www.reddit.com/user/wronglyzorro/)和 [Livelierepeat](https://www.reddit.com/user/livelierepeat/) 争论为什么后端开发者可能不喜欢 GraphQL。来自更有经验的开发者的回应获得了三倍多的支持票！此外，我选择了一个更长，但非常详细的审查。

[错误地佐罗](https://www.reddit.com/r/reactjs/comments/9nmj0w/what_is_your_experience_with_react_apollo_graphql/e7nkk73/) (12)说:

> 我们在网络应用上严格使用 react + apollo。我们也强迫移动客户端使用它。太不可思议了，未来。后端开发人员当然讨厌它，因为他们有自己的方式，不喜欢改变。然而，在过去的一年中，当出现任何形式的停机时，graphql 都不是弱点。总是遗留的后端服务出了问题。

[Livelierepeat](https://www.reddit.com/r/reactjs/comments/9nmj0w/what_is_your_experience_with_react_apollo_graphql/e7o92o5/) (40)回应道:

> 你可能想获得更多的洞察力。我曾经是一个年轻的开发者，使用所有最新的工具，嘲笑那些“不能适应”的人。我明白了，通常有比“人们讨厌改变”更有趣的原因。比如 GraphQl 会产生累赘的抽象吗？他们抵制的工作量增加了什么？
> 在某些时候，使用所有最新的工具会失去它的光彩。更多的力量来自于对代码和人员流程的理解。

[Capaj](https://www.reddit.com/r/reactjs/comments/9nmj0w/what_is_your_experience_with_react_apollo_graphql/e7nlgrn/) (11)详细回顾:

> 我们从五月份开始在生产中使用它。我们是一个完整的团队，所以我们不会任由其他团队做后台工作。说服每个人并不容易，但是由于 GQL 内置了一个简单的特性，每个人都认为它看起来比 REST 好得多。Graphiql 在这方面帮了大忙。已经很好了。我们在后端启用了 apollo 引擎，我真的很喜欢使用指标来搜索产品中的 API 错误。我们使用 decapi 来修饰 objection.js DB 模型。我们有一个定义模型的地方，GQL 几乎是免费生成的。
> 在前端，我们使用 apollo-client，但是到目前为止我们还没有使用缓存。我们的 FE 重点是摆脱我们遗留的 angular.js 代码，所以我们还没有时间试验 FE 缓存。
> 我不考虑使用 apollo 进行客户端状态管理，因为到目前为止我听到的所有反馈都是它还没有准备好投入生产。我还得说，对于它真正做的事情，它看起来相当冗长。相反，我希望我可以扩展[https://github.com/mhaagens/gql-to-mobx](https://github.com/mhaagens/gql-to-mobx)，并利用它来满足我们的国家管理需求。MST 用 typescript 创造奇迹。如果我们可以在编辑 GQL 查询的同时从查询中动态生成 MST 模型，我们就可以大大提高我们的生产率。

# 缓存

我从这里已经提到过的 [SwiftOneSpeaks](https://www.reddit.com/user/SwiftOneSpeaks/) 和 [Scruffles360](https://www.reddit.com/user/scruffles360/) 那里找到了很多很棒的好评。这是他们就缺乏缓存和潜在解决方案所讨论的内容。

[雨燕峰](https://www.reddit.com/r/reactjs/comments/bozrg1/graphql_vs_rest_putting_rest_to_rest/eno3ovb/) (23)写道:

> 虽然您可以将 GraphQL 配置为不同的工作方式，但实际上它们总是 POST 请求。这意味着所有依赖于 GET 是幂等的和 POST not 是 now 的浏览器/CDN/代理缓存在默认情况下都不能工作。一切都被当作一个新的请求。您可以设置客户端进行更多的智能缓存，但这实际上是在解决您自己造成的问题。

[scruffles 360](https://www.reddit.com/r/reactjs/comments/bozrg1/graphql_vs_rest_putting_rest_to_rest/enokkzb/)(11)resp oded:

> 阿波罗对此有一个解决方案。我没有尝试过，但是如果你想了解的话，它叫做动态持久查询。基本上，客户端使用查询的散列进行 GET 调用，如果失败，就返回 POST。下一个 GET 调用将成功并填充所有代理缓存。

# 数据抓取

这些人也提出了不同的观点。在最初的文章中，作者描述了一个有多个作者的博客应用程序的例子，以及使用 GraphQL 和 REST 的可能性。

[雨燕峰](https://www.reddit.com/r/reactjs/comments/bozrg1/graphql_vs_rest_putting_rest_to_rest/eno3ovb/) (23)说:

> 每个人都强调“过度抓取”的问题。我觉得这是设计糟糕的服务之外的一个转移视线的问题(这也指出了缺陷——不要指望来自糟糕的服务开发人员的 GraphQL 服务会突然变得不差),如果你把一个服务放在前面，这很容易解决——graph QL 可以是那个服务，但也可以是其他服务。问题不在于获取过多还是不获取，而是拥有一个中央服务并解决缓存问题。

[Scruffles360](https://www.reddit.com/r/reactjs/comments/bozrg1/graphql_vs_rest_putting_rest_to_rest/enokkzb/) (12)回应道:

> 过度抓取是一个真正的问题。当您有数百个客户端，每个客户端都以不同的方式调用您的系统时，向 rest api 添加一个属性会导致效率低下。例如，许多人指出，与 web 相比，他们的移动客户端使用以客户端为中心的外观，但这并没有扩大规模。我的项目被数百个客户调用，每个客户都以不同的方式要求略有不同的数据。

# 大男孩& GraphQL

每个人都对脸书、网飞和 Coursera 这样的大公司以及它们对 GraphQL 的改编感兴趣。在[文章](https://www.imaginarycloud.com/blog/graphql-vs-rest/)对 Reddit 的评论中，我们可以发现两个作为作者的主要原因。第一条评论是我发现的投票率最高的评论。

*   2010 年代初，手机使用出现了热潮，这导致了一些低功率设备和松散网络的问题。休息并不是处理这些问题的最佳方式；
*   随着移动使用的增加，运行客户端应用程序的不同前端框架和平台的数量也在增加。鉴于 REST 的不灵活性，很难开发一个单一的 API 来满足每个客户的需求。

[格鲁利希](https://www.reddit.com/r/reactjs/comments/bozrg1/graphql_vs_rest_putting_rest_to_rest/ennt7ak/) (62)回应文章:

> 这离题太远，毫无意义。一种不同的组织请求的方法，并不会使那些请求所在的网络变得更好或更坏。*   I think the author refers to endpoints rather than APIs, because any endpoint, no matter how many, is a part of APIs. If this is the case, why do we only need one endpoint?

[Scruffles360](https://www.reddit.com/r/reactjs/comments/bozrg1/graphql_vs_rest_putting_rest_to_rest/enohrog/) (16)回应格鲁利希:

> 前两点在文章中用词不当，但仍然有效。REST API 可以是通用的、可重用的，也可以是专门为一个已知的客户端定制的。在第一种情况下，当您不断向系统发出呼叫以获取更多数据时，您不会获得良好的性能(特别是在高延迟网络上，就像我们 10 年前在移动网络上一样)。如果你为一个特定的客户设计你的 API，你显然会遇到可伸缩性的问题。

# 总结

当选择正确的注释来总结 GraphQL 的状态时，有很多要说的或要选择的。迄今为止，reddit 上最受欢迎的内容是 facebook 或网飞的案例研究，但它们并没有得到太多评论。
这已经很好地总结了 reddit 对 GraphQL 的看法。从开发者的日常生活中，我无法跳过 [Kdesign](https://www.reddit.com/r/reactjs/comments/bozrg1/graphql_vs_rest_putting_rest_to_rest/enn9sdf/) (36 岁)写的:

> ### *GraphQL provides job security, which is certain* .

[Kollektiv](https://www.reddit.com/r/node/comments/bozsb1/graphql_vs_rest_putting_rest_to_rest/enng3ba/) (44)列举了一大堆 GraphQL 问题:

> *   It is difficult to inquire about things like speed limit and credit estimation.
> *   According to the type and the working mode of the data loader, if a complete module is not written for the query, it is difficult to effectively bind the query to the database layer by grouping the query.
> *   Validation only checks the type, so you still need some JSON pattern for additional format validation. GraphQL queries only allow left joins, so recreating SQL with filters, such as inner joins, quickly becomes awkward.
> *   Pagination (connection) imposed by frameworks like Relay is a mess.

关于我对 graph QL[SwiftOneSpeaks](https://www.reddit.com/r/reactjs/comments/bozrg1/graphql_vs_rest_putting_rest_to_rest/eno3ovb/)(24)的初步研究，我写道:

> 我认为我们看到的许多“GraphQL 是伟大的”报告主要是因为任何新服务都是伟大的——随着时间的推移，它们只会变得残旧，因为假设被违反，需求发生变化，代码发生变化。这并不意味着 GraphQL 不会很棒——只是意味着我不能太相信早期的报告。

最后，我选择了 [Mando0975](https://www.reddit.com/r/node/comments/bozsb1/graphql_vs_rest_putting_rest_to_rest/enopzpk/) (28)观点来总结这篇文章:

> 开发应该始终是为工作选择合适的工具。GraphQL 并不总是正确的工具。REST 没有死，GraphQL 也不会杀死它。

### 你对 GraphQL 有什么体验？

如果你正在寻找最好的 graphql 教程，请查看这个[帖子](https://blog.graphqleditor.com/top-graphql-tutorials-reviewed-2019)。