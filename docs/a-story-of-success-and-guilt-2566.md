# 一个关于成功和内疚的故事

> 原文：<https://dev.to/happydragos/a-story-of-success-and-guilt-2566>

开发者们，我想分享一个使用开源技术构建产品的成功故事。

我开始 [https://archbee.io](https://archbee.io) 作为一个宠物项目，想学习这个名为 SVG 的默默无闻的东西。当时我主要是一名 Java 后端开发人员，我认为我应该走出自己的舒适区，学习用它来构建一些很酷的东西。

这是一个软件架构图表解决方案。良好的...解决方案是太多要说，它几乎是可笑的，当我推出。但不知何故，有一小部分人喜爱它，并一直使用它。每天只有很少的流量来自我回答 Quora 的问题，但是足够让我保持精神状态。

pet 项目是一个非常快速的普通 Javascript 和 Jointjs(一个图表框架),位于前端和 bare Node.js 和 MongoDB 上。全部运行在德国某处的非常便宜的 10 美元/米的 Scaleway 机器上:)

在我确定我可以做出人们愿意为之付费的产品(开发团队的知识库)后，我开始改变我的技术选择，这样我就可以平静地工作 10 年，甚至让其他人为这个代码库做出贡献。

所以我做了。一切都用了严格模式的 TypeScript，并在 React 和 Next.js 中重写了前端。然后我有点讨厌 MongoDB，又花了一天时间将其改为 Sequelize + PostgreSQL。我找不到 PostgreSQL 的足够好的用户界面，我说让我们在开发中使用 MySQL & Workbench，当我部署时，我只需将 Sequelize 驱动程序改回 PostgreSQL。你可能知道，这不起作用，所以我坚持使用 MySQL -这就是我选择它的方式:)，但我很高兴结果是这样的，我非常喜欢它。然后，我意识到我需要让整个事情实时进行，以提供卓越的用户体验，我需要一个用于 PubSub 和 socket.io 的 Redis。

我意识到我骑在开源上，有时我为几乎没有回报而感到内疚。我总是说我喜欢开源，但是我的言行不一，现在也是如此。

其他人有这种感觉吗？

请在 ProductHunt 上查看我所说的产品的最新版本！我很乐意听到您的想法、问题和反馈！

[https://www.producthunt.com/posts/archbee-2](https://www.producthunt.com/posts/archbee-2)T2[https://arch bee . io](https://archbee.io)