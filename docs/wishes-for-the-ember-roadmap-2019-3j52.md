# 对 2019 年烬路线图的祝愿

> 原文：<https://dev.to/tleperou/wishes-for-the-ember-roadmap-2019-3j52>

我总是惊讶于 Ember 能让我完成多少事情。我用 1.13 版本创建了我的第一个 SPA。从那以后，我部署了一些 Angular 和 React 应用程序。余烬仍然是我的最爱。

😏

开箱即用的组件、数据和路由器边界的所有功能是一件了不起的事情。此外，ember-cli 使构建过程如此简单，您只需专注于您的功能开发。即使——大部分——其他框架在发布 Web 应用程序之前需要配置，但与 Ember 相比，它们的成功是毋庸置疑的。

我将我的愿望分为两类:

1.  战略性的，主要是发展 Emberistas 社区
2.  实用，提高开发体验

* * *

让我们深入到战略层面。

**一个高效的网站**

我对 Ember 的第一个关注是赢得客户、公司和开发者的信任。人们比以往任何时候都更了解网络技术的前景。因此，在适当的情况下，我很难解释 Ember 比其他框架更适合。我挣扎是因为我对抗信念。不幸的是，我反对潮流效应。不是工程问题。

😤

也就是说，我希望一个现代的网站有一致的指导方针，促进烬是一个可行的选择。

**比较和基准**

我的第二个关注点与 Ember 在前端生态系统中的位置有关。我不能忍受继续阅读 Ember 的基准或比较数据并做出反应。

这就像把一个工具比作一个工厂；一块有中国长城的砖；普拉达创作的羊毛球。话虽如此，但关注 GlimmerJS 并做出反应是有意义的；或者，Ember 和 React+React-DOM+React-router+React-redux。

🙌

Ember 及其社区应该为他们迄今为止在 SPA 生态系统中所做的贡献感到骄傲。功能、性能、开发经验和理念启发了其他人(当没有抄袭时)。从这个角度来看，它会说服新的开发者，让灰烬使者们放心。

我希望功能比较和基准性能与网站上的其他框架。

**余烬遍布全球**

我最后关心的是世界上灰烬的存在。亚洲和印度市场有大量的开发公司和潜在捐助者。那里的事情变化极快。他们是做什么的？猜，角，反应，还是 Vue。如果他们知道 Ember 的存在，他们会在他们的项目中使用它。他们几乎不知道这件事。

也就是说，我希望有一个多语言网站，包括“开始”。

* * *

现在，我想谈谈几个实际问题。

**Lazyloading 源代码**

对于一般的公共 web 应用，我选择 Angular。原因只有一个:它的 lazyloading 模块系统。即使对于面向移动的 web 应用程序，我也出于同样的原因选择 Angular。应用程序源的大小是一个关键问题。尤其是对于大数据量和低数据带宽使用率的应用。Ember 必须提供一个 Just Work 答案。

绣花很有前途。我必须告诉你，我对这个计划感到非常兴奋！也就是说，我的愿望是获得基于路由、附加组件或其他东西的 lazyloaded 源代码应用程序。

**作用域 CSS**

如果你是一个面向 utils CSS 的集成者，下面的段落不会让你感兴趣。

撇开纯抽象的组件，有人创建组件而不进行样式化吗？不，不可能。设计应用程序的样式是整个开发过程中的一项基本任务。它直接有助于项目的成功。用作用域风格的系统设计 web 应用程序应该不需要什么努力。

即使 ember-css-modules 是一个很棒的插件，我认为开发者应该只依赖 ember。我的愿望是得到一个原生的作用域风格的系统，它正好可以工作。

**静态页面渲染**

对于遵循 JAMstack 思想的简单网站来说，Ember 可能是一个有吸引力的选择。Ember-cli 应该提供一种生成所有路由的方法。不幸的是，prember 要求开发人员配置要呈现的路线。

我希望我们可以使用一个命令行在静态文件中呈现所有路由，无需配置，例如:

`ember render -env production`

**完全支持打字稿**

当谈到健壮的 web 应用程序开发时，Typescript 是不可避免的。它可能会得到开发人员的支持。

我希望插件和 Ember 应用程序的开发人员使用默认的 Typescript，不要在 ember-cli 上进行配置。

新 Ember 开发人员在轻松开发之前必须吸收许多概念。文档可以清楚地介绍每一层，此外，介绍整体。

**基础层文件**

Ember 是图书馆的天才组成*。它将它们联系在一起，为水疗中心的每一个底层提供支持:

*   组件闪烁
*   模板的模板手柄
*   路由器 Router.js
*   状态管理成员-数据
*   构建管道成员-cli

我希望文档能回答灰烬是由什么组成的？让开发者有机会实现他们项目的全部或部分堆栈。

(*)如果一开始不完全正确的话。现在，Ember 的某些部分已经成为独立的图书馆。此外，烬辛烷更进一步。

这就是我的全部。感谢核心团队和社区参与这次大冒险。

让我们一起让 Ember 变得更好。