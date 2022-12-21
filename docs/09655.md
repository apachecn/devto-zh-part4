# 2019 年我有哪些关注点？

> 原文：<https://dev.to/michaelfriedman/what-tech-has-my-attention-in-2019-4bj4>

首先，感谢您抽出时间！如果你对 Next.js 和 JAMStack 感兴趣，我有一份每周简讯，在那里我分享了我最近在这个主题上所学到的最好的东西。我甚至会钻研一些其他的东西，比如我正在读的东西和即将发生的事情。现在注册是完全免费的，所以如果你感兴趣，你可以在 [michaels.blog](https://michaels.blog) 注册

我指导过一些 React 开发人员，经常被问到人们在学习时应该把注意力放在哪里，以便在今天的就业市场上获得最大的相关性。

这篇博客文章是一个纲要，其中我集中了我的编程注意力，对为什么这样做的一点解释，以及一些对你开始使用这些技术有用的资源。如果您不熟悉现代 JavaScript 生态系统，并且希望现在就进入快车道，请密切关注以下内容。

1.  [React JS (NextJS，Gatsby)](#react)
2.  [GraphQL /阿波罗](#graphql)
3.  [亚马逊网络服务(λ)](#lambda)
4.  [TDD](#tdd)
5.  [还原](#redux)

## 做出反应

对 React 的深刻理解是几乎所有现代 web 开发人员的基本构件。我不会在这里花太多时间，我只是假设如果你是一名 web 开发人员或者想成为一名 web 开发人员，你已经明白你需要知道反应就像你的手背一样。

当我开始的时候，这不是一个给定的。新兵训练营的课程通常很难跟上行业趋势。我在学校的时候学过棱角和反应。对我来说，到了有角的时候(那是 2016-2017 年)，字迹已经在墙上了。今天，我很高兴地推荐 React 作为您的工具箱中唯一需要的前端库/框架，以适应当前的市场。你最好密切了解 React，而不是把时间分散在学习多个前端框架上。

React@16.8 为我们带来了一些强大的新 API，这将改变你创建 React 应用的方式。上课和生命周期的日子已经一去不复返了。现在，我们可以使用功能组件来设计真正可组合的界面，这些功能组件允许我们以比以前更加灵活和可扩展的方式直接从应用程序的状态中派生出我们的 UI。

### 下一个 JS

下一个是 React 框架，免费提供服务器端渲染。服务器端渲染允许 DOM 在页面加载时充分水合，这意味着代码比典型的 Create-React-App (CRA)应用程序更易于抓取和 SEO 友好。SSR 应用程序在搜索引擎索引中的排名非常高，因为搜索引擎更容易收集网站传播的信息的类型和质量。

NextJS 抽象了配置服务器端呈现应用程序所涉及的大量复杂性。如果这还不够的话，NextJS 已经添加了许多高性能的特性，这些特性在开发 React 应用程序时非常有用。NextJS 的最新版本现在支持现成的 TypeScript，不需要任何额外的配置。只需用`.ts`或`.tsx`来命名文件，就可以开始运行 TypeScript 了。

### 盖茨比

盖茨比是另一个 React 框架，但盖茨比的专长是静态网站。JavaScript / React 社区已经团结在 Gatsby 和 JAM Stack 周围，将其插件库扩展到 1200 多个包。正确开发的 Gatsby 站点速度很快，并且在网络连接中断时保持高度可用。Gatsby 依赖于 GraphQL 来查询其 API，这种 API 已经被如此迅速和广泛地采用。

### 反应资源

*   [反应钩子](https://reactjs.org/docs/hooks-overview.html)
*   [学习 NextJS](https://nextjs.org/learn/basics/getting-started)
*   [下一个例子](https://github.com/zeit/next.js/tree/canary/examples)
*   [盖茨比教程](https://www.gatsbyjs.org/tutorial/)
*   [盖茨比简介](https://frontendmasters.com/courses/gatsby/)(必须是 FEM 成员)

## GraphQL

GraphQL 是我作为软件开发人员的职业生涯中见过的最激动人心的后端开发进步。它重写了关于现代后端应该如何设计的书。它允许我们的客户雄辩地描述他们操作所需的数据，这是一个直到现在还没有真正解决的问题。能够从单个端点获取所有数据，完全改变了我们组织数据的方式。它还允许增量采用，因此它不是全有或全无的买入，这也有助于它的快速采用。

### GraphQL 资源

*   [学习图解法](https://www.howtographql.com/)
*   [一张图](https://onegraph.io)
*   [Github API v4](https://developer.github.com/v4/)

## λ

我们现在处于无服务器计算的时代。以过去成本的一小部分服务数百万客户，意味着尚未迁移到这种模式的企业在未来五到十年内肯定会迁移。这包括银行业等 20 多年来没有进行基础设施改造的行业。整体后端正变得越来越过时。运行无服务器架构的经济利益正促使技术巨头以看似快速的连续方式重写他们的整个 API。AWS Lambda 和 API Gateway 是现代无服务器计算技术的核心。

AWS 云从业者认证是从 AWS 开始的一个很好的地方。我只提 AWS 是因为它的市场份额。如果你有理由使用另一个提供商，无论如何，学习谷歌或 Azure。

一种方法是依靠我们的同事来构建出色的开发工具，这样您就永远不必看到 AWS 管理面板的内部。也许更好的方法是理解您至少应该对云有一个很好的了解。

### AWS Lambda 资源

*   [API 网关资源](https://aws.amazon.com/api-gateway/resources/)
*   [AWS 培训](https://www.aws.training/LearningLibrary?&search=lambda&tab=view_all)
*   [无服务器框架](https://serverless-stack.com/#table-of-contents)

## TDD

如果你在 React 生态系统中实践敏捷开发，这可能意味着你可能会使用 Jest、React 测试库和/或 React Storybook。大多数 React 商店希望初级员工至少在其中一两个方面有丰富的经验。单元测试是你应该深刻理解的东西，这经常被新兵训练营所忽视。

### TDD 资源

[Jest](https://jestjs.io/docs/en/getting-started)
[story book](https://storybook.js.org)
[React 测试库](https://github.com/testing-library/react-testing-library)
[5 题每个单元测试必答](https://medium.com/javascript-scene/what-every-unit-test-needs-f6cd34d9836d)

## Redux

等等，我以为钩子的意思是我们不再需要 Redux 了？不完全是。随着 React v16.8 中钩子的发布，本地状态管理确实有了突飞猛进的改善。然而，许多大规模的复杂应用程序仍然需要确定性 UI 的可靠性。对潜在雇主来说，熟悉这些技术将是至关重要的资产。这将是在你的熟练程度上把你区分为中级或高级的事情之一。

### Redux 资源

丹·阿布拉莫夫的知识分子课程