# 作为新开发人员使用 API 的初级读本

> 原文：<https://dev.to/suesmith/a-primer-on-the-reality-of-working-with-apis-as-a-new-developer-ej>

作为一名软件开发人员，今天的现实使得集成数据和服务成为一个主要关注点。如果你刚刚完成了一门编码或计算机科学课程，当你开始从事一个现实世界的项目时，你可能会发现自己在和 API 打交道。不幸的是，在教育环境中，我们有时会对开发人员的日常工作产生错误的印象——API 就是一个很好的例子。在这篇文章中，我们将会谈到当你开始在真正的应用程序上工作时可能会遇到的一些问题，并提供导航 API 生态系统的技巧。

# API(消歧)

首先，澄清一下:术语 API 用于指代许多不同的事物，并且它的重点在过去几年中已经发生了很大的变化。如果你学过面向对象开发，你可能对 API 很熟悉，它是代码组件，有明确定义的接口，允许“客户”代码访问系统中的特性和功能。这些 API 通常涉及对象和方法/函数。*这是**而不是**我们将在本文中讨论的 API 类型。*😅

在本文中，我们将讨论用于管理通过 web 访问数据和服务的 API。如果你遇到过术语 HTTP API 或 REST API，那就是我们正在讨论的。通过这种类型的 API，客户端应用程序向**端点**发出**请求**，以对数据执行操作。一个例子是一个商店的数据库，它有一个 web 应用程序和一个移动应用程序——有一个 API 管理对这两个应用程序的数据访问。

> 更令人困惑的是，您还会看到“Web API”这个表达，用来指代这两种类型的 API。在客户端，Web APIs 通常是指浏览器技术，您可以使用 JavaScript(例如 DOM)与之交互。在服务器端，Web API 将是我们在这里讨论的类型。😭

API 可以支持一系列操作，以检索、插入、更新和删除数据(更一般地说是提供服务)。在大多数情况下，需要对 API 请求进行身份验证，有多种身份验证模型可供选择。API 有时提供支持资源来帮助集成，例如文档、规范和定义(每一个都满足稍微不同的标准😩).

## 消费者和发布者

如果你是一名软件开发人员，你可能会使用 API，根据你所做的开发类型，你也可能会构建和发布 API。通过利用外部来源的数据和服务，开发团队可以更快更有效地创建复杂的产品。例如，如果有人已经把真正擅长消息传递作为自己的事业，那么接入他们的服务(希望能不断改进)并把更多精力放在你正在开发的系统的独特部分是有意义的。

您还可能会使用与 API 相关的[微服务](https://nordicapis.com/what-is-the-difference-between-apis-and-microservices/)——但是微服务是关于在应用程序中划分服务的，API 更多的是关于您作为开发人员如何访问服务。好了，让我们离开这个语义兔子洞，转到更实际的关注点。🎉

# 问题

[![Get Overwhelmed](img/1154d506852c23ba802306327b64157a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w8oeEGGV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/6666370/61358333-01b6b500-a872-11e9-8682-e7169ef6eddb.png)

在教育中，我们通常有相当严格的约束，比如学习成果，这些约束塑造了学生的体验。这让我们能够找到想要的目标，并帮助学习者获得一套特定的技术技能。不利的一面是，它会让学习体验变得与在野外获取和应用新的编码技能的现实很不一样。

这不仅仅是来自训练营和 CS 课程的新程序员的问题，这是开发人员软件工具教育中的一个普遍问题，*然而，我们正在慢慢地更好地创造更真实的学习体验*:

> ![Angie Jones @ #OSCON profile image](img/387d7791facf6a26977acd603ff6337b.png)安吉·琼斯@ # OSCON[@ tech girl 1908](https://dev.to/techgirl1908)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)在我的视频课程中，有时我正在播放的东西不起作用。我以前会把这些删掉。现在我把它们留在里面，大声朗读错误消息，解释它的意思并演示如何修复它。我想这是有价值的...如果不外乎显示甚至“专家”击中问题。[twitter.com/Madisonkanna/s…](https://t.co/57jBDT7VQx)2019 年 7 月 14 日下午 16:03麦迪森坎纳[@麦迪森坎纳](https://dev.to/madisonkanna)看着一个高级开发人员被一个 bug 卡住，然后走完他/她的修复过程，是一个初级开发人员可以见证的最有帮助的事情之一。你学习他们思考问题的策略，你也意识到每个人都有陷入困境的时候。[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1150435569723293698)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1150435569723293698)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1150435569723293698)

# 为什么这很重要

[![Delete Assumptions](img/506ecc8f15bd2039f0199a89b87e5ca1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--llH-L7KO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/6666370/61358337-01b6b500-a872-11e9-852b-881cdfc6e77e.png)

集成服务是当今软件开发的定义特征之一——它不仅仅是一项活动，它可以决定产品的整个方法。由于这个原因，开发人员关系领域(DevRel)在过去几年中已经出现并得到了巨大的发展。

在过去，一个典型的软件开发人员可能会为不是开发人员的最终用户开发应用程序，现在有一个很大的机会，至少你的一些用户将是其他开发人员，他们希望将他们正在进行的任何项目与你自己的项目相集成——通常通过 API，但也通过 SDK、扩展、开源计划等。

所有这些都意味着，对于大多数开发人员来说，导航依赖关系和连接的网络将是工作中至关重要的一部分(更不用说产品和工程团队的其他成员，如 QA 工程师，甚至在某种程度上是项目经理)。

# 好吧，那么现实是什么呢？

那么，使用 API 会涉及到什么，是你在从事软件行业之前可能没有遇到过的？*请随意分享你自己的恐怖故事……*😉

## 大量集成

在粒度级别上，您可能会发现自己正在构建或使用一个 API 但事实上，如果您在任何种类的大型软件系统上工作，您可能会与多个外部服务集成。让所有的部分都很好地相互配合是一项相当艰巨的任务——也是一项令人沮丧的任务，因为您实际上只能直接控制(或者甚至影响)团队内部正在开发的部分实现。

理论上，所有这些集成都应该由自动化驱动，服务之间的数据完全可以互操作，但遗憾的是情况并不总是如此。

## 现实是凌乱的

[![Patch Mess](img/29442536791a838ad6feaf834a79c37a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pzWWqCgR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/6666370/61358340-024f4b80-a872-11e9-915a-e9ecbb56f933.png)

一旦您开始使用外部服务，无论是来自另一个组织公开的开发人员工具，还是您可能正在为其构建应用程序的客户带来的数据，您都会发现情况往往会变得混乱。如果你为一个更大的组织工作，这甚至可能发生在内部 API 上。🙊)

作为开发人员，我们喜欢自动化事物，尤其是重复的任务，但是当您陷入来自业务环境的真实数据时，您会发现自己经常与不可预测的混乱信息搏斗，这使得自动化极具挑战性。

如果有可用的文档——*，但通常没有*——你会经常发现它不完整或不准确。

> 对于我的前雇主来说，这是一个特殊的问题，在那里我们使用 OpenAPI 规范来自动化与客户端数据的集成，但更多的时候，规范所指示的内容与 API 请求的实际行为之间存在差异。从消费而非公开 API 的软件的角度来看，看到 DevRel 的快速增长部分是由解决开发人员参与和采用中的这些问题的需求所推动的，这很有趣。

当涉及到与客户合作时，除了这些挑战之外，还有一个事实，那就是你可能根本没有与构建 API 的人打交道——这个人可能继承了它，或者从第三方来源集成了它，而他们对它的理解/控制可能是最少的。

### 侧栏:这是一个有用的类比

这里值得指出的是，这种混乱的数据问题类似于许多编码学习经验中另一个缺失的成分——集成的现实，不是与数据，而是与人。作为一名开发人员，您将面临的许多最大的挑战将与您如何与您的团队、社区、客户、合作伙伴等等打交道有关。在一个竞争/变化优先权的商业环境中运作意味着在一个不断变化的，经常是非常动态的环境中交付你的工作。

就像我们在工程工作面试时假装不去谷歌所有的东西一样🙄，在大多数开发人员教育资源中，我们的行为就像是在真空中操作一样。事实是，你需要将你的任务整合到一个系统中，这个系统有各种各样的参数在你的控制之外。

# 提示和工具

[![Put Tools](img/1246f2b7a923543d5d08ca0827ac0c7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z1fMaH5y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/6666370/61358339-024f4b80-a872-11e9-87bc-78758beca083.png)

好消息是开发人员一直在成功地处理这种事情——否则我们就不会有很多日常使用的应用程序了。幸运的话，你会和其他更有经验的人在一个团队里，一起克服整合的困难。如果你有机会，观察一个高级开发人员如何处理[依赖地狱](https://medium.com/better-practices/conquering-the-microservices-dependency-hell-at-postman-with-postman-part-1-introduction-a1ae019bb934)是非常宝贵的。

然而，作为一名开发人员，你不可避免地要花大量时间自己解决问题——所以这里有一些技巧和工具，可以帮助你战胜任何会绊倒你的 API 开发和集成恶魔。

## 不信任任何人

[![Post Tests](img/5b981de214752160426252862a63939b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FQehr6eT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/6666370/61358338-01b6b500-a872-11e9-85fb-91ee4236c319.png)

只是开玩笑，但是在处理 API 的时候，一点健康的怀疑是必要的。你不能太相信文档(即使最好的团队也很难保持这些资源 100%最新)。当你的应用程序依赖于其他来源的数据时，测试变得越来越重要。

在某些情况下，您甚至可能使用从后端系统自动输出的规范或定义，在这种情况下，您可能会认为它可能与 API 的实际行为相匹配——遗憾的是并不总是如此。意外返回的空值可能会破坏应用程序。

谢天谢地，有一些很棒的开发工具可以帮助 API 开发、测试和发布过程的各个部分:

*   [Postman，主要与测试](https://blog.getpostman.com/2017/08/23/integrate-api-tests-with-postman-newman-and-travis-ci/)相关，但支持 API 开发生命周期不同部分的各种工作流，包括协作和文档。
*   包含多个实用程序的 [Swagger 工具集](https://swagger.io/resources/webinars/getting-started-with-swagger/)，例如从一系列语言中的规范和 codegen 存根开始，用于 API 服务器实现和客户端库构建。
*   [Stoplight](https://stoplight.io/blog/getting-started-with-api-design-using-stoplight-and-openapi-90fc8f37ac2e/) ，特别是如果你的 API 开发项目是面向规范的，比如围绕 OpenAPI。

有很多 API 工具，这些只是我用过最多的——请在评论中分享你的！

实际上，花点时间学习 API 开发和测试工具是值得的，就像你学习一门新的语言或框架一样——这些是你会发现自己会一次又一次用到的技能。就即将到来的熟练程度而言，很可能值得研究一下 GraphQL，因为它似乎越来越受欢迎。

### 关于标准的一点说明

如前所述，如果您处理 API 文档、规范和定义- [所有这些都与](https://swagger.io/resources/articles/difference-between-api-documentation-specification/)相关，但执行的功能略有不同，那么另一个困惑点就在您面前。他们都专注于指出一个 API 做什么，但是在相对程度上有所不同[信息被传达给人类或机器](https://nordicapis.com/difference-api-documentation-specification-definition/)。

*   文档是供人们使用的，本质上包括 API 用户指南。
*   你可以把一个规范看作一个契约，作为一种 API 行为的共享描述。OpenAPI 规范是一种以这种方式描述 API 的常用标准，采用 JSON 或 YAML 格式。
*   定义也描述了 API，但是专门用于机器处理。

然而，尽管围绕 API 已经有了很多推进标准化的努力，但还没有真正的共识可言(至少在现阶段是这样)。此外，编写规范等不一定是一项简单的任务，您可以看到灵活且支持不同文档类型/格式的 API 工具的好处。

## 你的用户可能是其他开发者

[![Get Service](img/ef6fdfabe28fd29b8cb7b5ff2a225119.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZMkMCyik--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/6666370/61358330-011e1e80-a872-11e9-9b49-847b4a909851.png)

如果你公开一个 API 或者开发者服务，你会发现你把其他开发者当成了你的用户。这意味着[你将进入 DX](https://nordicapis.com/dx-should-be-a-priority-reveals-state-of-api-report-2019/) (开发人员体验)的世界，这带来了它自己的一系列挑战，这些挑战[不是为前端或 UX](https://medium.com/better-practices/design-apis-like-you-design-user-experience-a7adeb2ee90f) 保留的，因为它们从 [API 设计和结构化阶段](https://devrel.net/developer-experience/applying-visual-design-principles-to-api-design)开始发挥作用。

> 请注意，即使您正在开发一个供团队或公司内部使用的 API，考虑 DX 也是值得的(就像代码注释一样，当您试图使用不久前开发的服务，而您的记忆又不是那么清晰时，您可能会庆幸自己想到了 DX)。🤓

DX 本身是一个完整的领域，但是因为开发教育是我的事情，这里有一些快速的要点:

*   开发人员的背景千差万别——不存在可以假设的正常技能组合。
*   开发人员的背景也是多种多样的——从在企业工作的人到自由职业者。您可能有大量的项目与您的 API 集成。
*   有了开发人员工具，社区成为一个重要的组成部分，入职培训需要为开发人员量身定制。同样，这些都是完全独立的专业领域，因此有了 DevRel，但是如果你碰巧参与了开发人员门户或学习资源的构建，这里有一些要点:
    *   支持“快速启动”途径，允许新用户快速尝试请求，而无需注册和等待 API 密钥。
    *   如果你的 API 支持很多不同的用例，那么创建途径来帮助来自不同背景和不同需求的人开始。
    *   尝试使用您的 API 为示例实现提供完整的演练——一个常见的错误是只包括解释端点的参考文档，而没有传达各部分在实践中是如何组合在一起的，因此经常缺少上下文。
    *   这对你来说似乎是显而易见的，但是要确保你的 API 是做什么的。😲这同样适用于解释任何特定领域的术语。
    *   获取反馈并为 API 用户提供贡献途径——能够产生影响通常对开发者来说很重要。
    *   尽管面向开发人员很重要，但是请记住，其他利益相关者可能会使用您的资源——您可能会遇到与 API 相关的业务语言，比如“数字转换”对于开发产品，开发人员可能是用户，但组织中的其他人可能是购买者。

# 结论

当谈到 API 对于当代软件开发有多重要时，我们在这篇文章中提到的只是冰山一角，但希望它能给你一些提示。好的一面是，这些集成让您可以最大限度地利用自己的开发努力来创建创新的应用程序。

随着集成成为开发者领域的一个关键元素，我们很可能还会看到更多致力于促进这些集成的工具和产品，更不用说关注开发者体验的公司的持续增长了。就这一点而言，如果你发现自己对每天整天写代码之外的事情感兴趣，你可能想考虑自己在 DevRel 中的职业生涯！🚀