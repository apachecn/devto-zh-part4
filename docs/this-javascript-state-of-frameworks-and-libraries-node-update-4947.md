# 这个。JavaScript-框架和库的状态-节点更新

> 原文：<https://dev.to/thisdotmedia/this-javascript-state-of-frameworks-and-libraries-node-update-4947>

由这个主持。JavaScript，这是一个在线活动，开发者可以从中了解 JavaScript、框架和库的最新消息，涵盖了框架领域的所有突发新闻。

2 月 19 日，State of Frameworks 的演讲者，包括许多来自开发世界的最优秀和最聪明的人，给了我们关于所有框架的更新。

[Node.js 技术指导委员会联合主席、](https://twitter.com/mhdawson1) Node 合作者和社区委员会成员麦可·道森与 Node 上的许多工作组和 IBM 的团队合作，他分享了他对 Node . js 未来的想法

[![](img/176d7340a30e27d76b514287a4b3ab85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tNudTM_0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3200/0%2AfpMdb5okbKu_z2_I)

## 麦可·道森——海训方案共同主席，节点。JS — @mhdawson1

[![](img/3b3fc2482587e0a19051a43cbf70a2a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fZhgQW-G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3200/0%2AvdnTDPY6c9CrpL3q)

## 即将发布

Node.js 遵循常规的发布节奏，这意味着将在 2019 年 4 月进行一些重大过渡。这些将包括以下变化。

### 节点 6。X

6.x 将于 4 月停产。如果你还没搬到 6 楼。x，确保立即这样做。

### 主动 LTS 发布消息

节点 8。x 是一个活跃的 LTS 版本，已经使用了一段时间。为了配合 OpenSSL 1.0.2 生命周期的结束，我们必须从活动阶段转移到维护阶段。这意味着 8.x 中的更改现在将只关注关键修复和安全修复。

节点 10。x 具有显著的性能改进和新版本的 npm。它还拥有像 http/2 这样的新特性。

同时节点 12。x 将于 4 月发布，然后过渡到 10 月的下一个 LTS。

最后，当前的版本是 Node 11.X。它有一个较新版本的 V8，也有不带标志的 workers，仍处于试验阶段。11.x 还具有报告功能，有助于诊断过程。

[![](img/182df9270183e180322352959b302aec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YEny9kk5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3200/0%2AN4CRQv4plr_Lr-aY)

## 十大关键新功能。X

节点 10。x 有许多显著的新特性，可以改善性能和用户体验。

节点 10。x 包含 N-API，这极大地简化了节点开发和部署。这是一个标准的 API，您可以使用它来构建本地附加组件。这将在所有 LTS 版本中提供，并且已经在两个 8.0 版本中保持稳定。x 和 10 . x . 4 月份会在所有版本中稳定。

节点 10。x 还具有 http/2 特性，它允许请求/响应复用、高效压缩和服务器推送。

另外，10。x 已经打开了 SSL 1.1.0，而 open SSL 1.1.1 已经在 master 中打开。然而，TLS 1.3 引入了一些问题，所以不能保证。开发人员仍在努力，希望他们能在未来解决这些问题。

## 进行中的特性

[![](img/e7bf88a54618694f00719bc723442407.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bhpr5Fn5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3200/0%2Ag5l6jDQb5XANfDIl)

Node 目前正在开发的一些特性包括 Workers，这在内核中是实验性的。

节点报告是一种诊断功能，过去可以作为本机模块使用，但由于它需要编译器，因此带来了许多挑战。为了解决这个问题，它被集成到 Node.js 的核心中..

ES6 模块也在不断开发中。节点工程师希望提供一个实现，在保持实现和现有用户满意的同时，提供尽可能兼容 ES6 的东西。

核心承诺 API 仍处于试验阶段。一些 API 返回了承诺，但是开发人员仍然在考虑这些 API 在未来可能会是什么样子，以及如何最好地实现它们。

最后，异步钩子也在为 Node 工作。这些是诊断特性，您可以使用它们来跟踪异步发生的事情，它们也仍然处于实验阶段。

[![](img/4ae7982d016d45a76a9c1a4c5b8b0d16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_9w3Ct4K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3200/0%2AYyBlNb0ZWXKmgwtm)

## 更上工

Node 的工人是基于 Web 工人的，虽然他们之间的区别还没有在文档中指出，但我们正在努力

在 11.7 或更高版本中，Workers 还不需要标志，但它仍然是实验性的。但这更多地反映了 API 已经有一段时间没有太大变化了。

在 Workers 中，每个线程都是一个独立的 JS 执行环境。您可以通过消息传递在这些环境之间交换数据。默认情况下，您将获得对象克隆。您还可以进行移交，将对象从一个主线程转移到一个工作线程。在这些情况下，您只能从一个线程或另一个线程访问对象，而不能同时从两个线程访问。

[![](img/b17f1061f8be5997519e36372f027127.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uwBEb3D7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3200/0%2Ae8OErOz-qeCnR1y0)

内存共享，即通过原子传递 SharedArrayBuffers 和管理并发性，现在也是可能的。

尽管这个特性很有用，但还是有一些限制。例如，您还不能转移手柄。并且启动工作线程仍然有开销，所以此时仍然推荐重用和池化。

[![](img/3597ee1308e980ed91fbc830616828b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nc-8i8ft--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3200/0%2A8SD3DGehhdMjcGhz)

[![](img/e15697850938d092ed89e0ef348901a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cr_m5egt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3200/0%2AMpGY-KcPN-uonF3B)

## 报告

节点开发人员一直在不知疲倦地改进诊断过程。仍处于试验阶段的诊断报告功能现已集成到节点核心中。

该报告将为您提供有关您的环境的重要信息。它是一个轻量级的，易于阅读的格式，并作为一个伟大的诊断过程的第一步。

在即将发布的 Node 中寻找更多关于报告的开发。

[![](img/0f61f9cc92c00b5cdb416f592b2ece65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oAZ7CkqQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3200/0%2AOUKKSMg73xD9YNOG)

## 其他举措

Node 并不全是功能。社区也在积极努力改善用户体验的许多其他方面，并解决问题。

例如，许多节点模块被严重依赖。但是模块维护人员可能已经离开了项目，或者可能没有时间来维护它们。有时，消费者需求和维护人员的可用时间不匹配。社区正试图通过包维护计划和团体来解决这个问题。

许多其他社区团体、聚会、委员会和项目都致力于深化节点安全网，增加参与，并通过更大的便利和强有力的决策支持最终用户。谈到 Node 的光明前景，安全性、自动化和管理是重中之重。

[![](img/7308bc4c1fe2b79f385f8842b728f5ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QONtBlew--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3200/0%2AU47--oXCLD2AQodO)

## Node.js 基金会和 js 基金会协同

10 月，有一项倡议将 JS 基金会和 Node.js 基金会合并在一起。Node.js Bootstrap 委员会一直致力于文档工作，以使这成为现实。如果你对联合基金会感兴趣，请点击此处了解更多[！](https://venturebeat.com/2019/03/12/node-js-and-js-foundations-are-merging-to-form-openjs/)最近[才宣布合并](https://js.foundation/announcements/2019/03/12/introducing-the-openjs-foundation)。

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。