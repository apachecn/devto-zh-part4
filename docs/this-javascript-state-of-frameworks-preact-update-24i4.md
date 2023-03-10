# 这个。JavaScript:框架状态-预先更新

> 原文：<https://dev.to/thisdotmedia/this-javascript-state-of-frameworks-preact-update-24i4>

由这个主持。JavaScript，这是一个在线活动，开发者可以从中了解 JavaScript、框架和库的最新消息，涵盖了框架领域的所有突发新闻。

[2 月 19 日](https://www.youtube.com/watch?v=DFF9eOlTWzY)，State of Frameworks 的演讲者，包括许多来自开发界的最优秀和最聪明的人，向我们介绍了有关所有框架的最新情况。

以下是 Preact 核心团队成员[杰森·米勒对 Preact 中即将到来的](https://twitter.com/_developit)[所说的一些话。](https://preactjs.com/)

## 杰森·米勒— Preact 核心团队，谷歌— [@_developit](https://dev.to/_developit)

## 预动作 X

Preact X 指的是一项已经进行了一年的计划，旨在重新设计 Preact 的工作方式。这种重写并没有从现有的代码库中提取任何代码，而是从 Preact 开发人员到目前为止学到的所有东西中收集，并保持 Preact 致力于的相同的权衡。

Preact 首先青睐尺寸，其次才是性能。对于 Preact X，开发人员专注于使库的其他特性和特征可用，但总是受到那些约束。

Preact X 是由名为“片段”的 Preact 概念的存在触发的，开发人员试图在 Preact 中实现这一概念，但没有成功。碎片可能会破坏图书馆。开发人员的反应是开始彻底重写——Preact x。

[![](img/84e9272f343883212d5b7bc9f3f5d1e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y0Vu6tCu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2122/0%2A8sg8qAwtk5o-8M8J)

### 预测 X 个目标

Preact X 的目标包括更好的特性、与 react 生态系统更好的兼容性、同时更小的库大小、更好的性能和更少的边缘情况。

Preact X 的重写始于一个私有报告中的实验。从那时起，20 多名开发人员一直在不知疲倦地重写 Preact。Preact 核心团队的许多关键合作者为重写做出了贡献。私人回购成为 Preact 前进的方式。现在，每项功能都在稳步走向免费。

[![](img/ef683c0467771377f9f5ac0d957e69d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qzvrgVew--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2106/0%2AIMCECGAXwgdOPwPH)

### 预动作 X 的变化

Preact 现在有了钩子，包括它们的多个实现——超过五个，尽管只有一个被发布。

用户现在可以从 preact/hooks 导入钩子。这将使你避免注射成本和布线成本，如果你不使用挂钩。它们都是完全独立实现的，所以你只需要为你实现的那些付费。

[![](img/8133611a5240c98a7ae6025190ec1d9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---hgviO5a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2106/0%2Ar69Dz5pkL5fqqzVo)

碎片是重写的主要原因。片段本质上是虚拟的 DOM 节点，当真实的 DOM 被渲染时，它们并不在真实的 DOM 中呈现——类似于[文档片段](https://developer.mozilla.org/en/docs/Web/API/DocumentFragment)，但是它们是持久的。在 Preact 的新 diff 中，这是可能的。

这一点很重要，因为 Preact 曾经将所有虚拟 DOM 节点与真实 DOM 节点区分开来。在 Preact X 存储库中，开发人员现在将虚拟 DOM 树与渲染函数生成的虚拟 DOM 树的新副本进行比较，然后将其应用于真实 DOM。

重写要解决的另一个问题是与 React 的兼容性。最初，重新实现是在与 Preact Core 相同的规范下开始的。但是因为尺寸被充分减小，这些问题中的一些可以被退回。

[![](img/32f47f57f5c5f34182e82fbcc6f06c70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JDS_q71n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2124/0%2AQej3uuW-g5TZQJiv)

Preact 中的虚拟 DOM 节点总是与 react 中的有很大不同——名称、形状和功能都不同。这一点在 preact-compat 中有所缓和。现在，Preact X (Ceviche)中的虚拟 DOM 节点看起来和 react 中的一样。

增加的其他兼容性工作包括 createRef()、createContext()、hydrate()、forwardRef()和其他函数。

最终的结果是 preact-compat 还是会存在，但是会大大减少。它曾经是一个 3KB 的库，位于一个 3KB 的库之上，但现在它明显变小了。

[![](img/24b9b4a9909826624bc515fd6f610db9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3-Xi7B4t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2098/0%2A8ngjDqRfK0rpLN0l)

## 生态系统工作和下一代

Preact CLI 最近的更改包括:发布 Webpack 4、差异 JS 服务、prerender 配置中任何路由的关键 CSS 内联、更简单的新配置格式、更快的安装和启动，以及切换到面向服务人员的 Workbox。

未来，Preact 开发人员还将关注编译器优化，支持 htm 作为首选选项，以及服务器端渲染的官方解决方案。

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。