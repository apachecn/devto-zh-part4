# 在 React 中重写草稿 Ember 应用程序

> 原文：<https://dev.to/cpow/re-writing-drafts-ember-app-in-react-58e5>

使用 React 最佳实践和工具，我在 9 个月内帮助重写了 DRAFT 的 web 应用程序。这是如何完成的概述。

* * *

## 客户端

[DRAFT](https://draft.com/) 是一家每日幻想体育公司，以独特有趣的 DFS 体育游戏和格式为荣
。他们有从足球到高尔夫的多种体育博彩游戏。2017 年，他们被 Paddy Power Betfair 收购
，自 2018 年以来一直作为草稿(由 Fanduel)运营。DRAFT 主要关注他们的 iOS 和 Android 移动应用程序，但他们也有一个非常广泛的网络应用程序，它补充了最初在 Ember 中构建的移动应用程序。

## 问题

2018 年初，由于对工程团队缺乏兴趣，缺乏可用的人才，以及来自 PPB 的新投资，DRAFT 开始在 React 中重新编写他们的 Ember 应用程序。DRAFT 有一个健壮的 Ruby on Rails API，服务于移动应用程序和 web 应用程序；然而，由于团队缺少 Ember 工作，新 API 调用的采用正在减少。前端客户端(Ember web 应用程序)错过了新的功能和新的游戏格式。

## 解

布朗树实验室最初受雇于 Ruby on Rails API，为即将到来的足球赛季做准备。在 API 团队工作 3 个月后；然而，很明显，新的 React 团队需要帮助来完成重写项目。在很短的时间内，
您真正在开发一个非常大而复杂的 React 应用程序，尽可能快地实现特性。我和团队使用了几个主要的
概念来快速构建特性并维护优秀的代码:

*   反应
*   原子目录结构
*   状态管理的 Redux

### 做出反应

[React](https://reactjs.org/) 是 facebook 编写的一个令人惊叹的 javascript 组件库。React 使得为你的项目编写
可重用组件变得非常容易。

草稿在他们的 web 应用程序中有很多重用。想象一下“玩家卡”的概念。“球员”可以是在 NHL、
NBA、MLB 或 PGA 打球的职业运动员。在重写过程中，我们创建了一个
“玩家卡”的概念，它囊括了所有这些场景，我们能够在整个应用程序中共享它。通过在 React
中创建可靠的组件，并在不同的地方重用它们，我们很快实现了之前的 web 应用程序
的许多特性。

### 原子结构

对于这个项目，我和我的团队使用[原子设计
模式](https://medium.com/@janelle.wg/atomic-design-pattern-how-to-structure-your-react-application-2bb4d9ca5f97)
来构建应用程序。如果你不熟悉这种模式，它看起来
像这样:

```
atoms - molecules - organisms - templates -> pages 
```

当应用于 React 项目时，原子结构模式工作得非常好。在
React 中，你以组件的形式创造一切。有了原子结构，你可以想象不同大小的组件整齐地装配到结构的各个部分的 T2 中。

[![Player Card](img/e24b446863b5c37623040f0e91bcdb84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CI9EUJnA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9qrnc8fg4v9isqevvkx1.png)T3】

<center><small>The player card -- an organism</small></center>

从上面想象我们的玩家卡。这个
组件实际上有很多部分。仅在我们的玩家卡中，就有一些组件跨越了我们原子
结构的多个层次！我们有一个玩家头像，它源于一个
`avatar` *原子*组件。我们有一个统计表，它来自一个
`statList` *分子*成分。我们将所有东西放在
`playerCard`中，它恰好是一个*有机体*组件——一个
将分子和原子结合起来形成一个更复杂的 UI 的组件。

[![Player List](img/e557fd53f06ee59eac474feef3e1c93a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N8sUJOTJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0y8eptlpovr7ou9qu2zj.png)T3】

<center><small>the player list -- a template</small></center>

`playerCard`组件可以被组合成一个玩家列表。这个列表
可以被认为是一个*模板*。你可以把这些模板组合在一起
形成一个页面。在上面的图片中，我们可以看到显示
赢得的比赛和过去比赛分数的页面。

[![Results Page](img/42e25cb1344334010f7c046f4d287ab6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7W6kAUta--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n8gbqb9jxedor1b068w8.png)

<center><small>the results page -- a page</small></center>

使用集合结构帮助我们很快地开发出 UI 组件，因为我们对如何通过代码将事物组合在一起有了很好的想法。我们
也能够快速重用大块的功能，因为我们把
的代码分割成整齐打包的组件，比如有机体和模板。

### Redux 为状态管理

Redux 是一个管理整个 Javascript 应用程序状态的神奇工具。摘自 Redux
网站:

> Redux 是 JavaScript 应用程序的可预测状态容器。它帮助您编写行为一致、在不同环境(客户机、服务器和本机)中运行的
> 应用程序，并且
> 易于测试。最重要的是，它提供了出色的开发人员体验，例如结合了时间旅行调试器的实时代码编辑

Redux 与 React 一起使用 [React-Redux](https://react-redux.js.org/) 包非常好。

在应用程序草案中，我们希望保持页面需要一组特定数据的概念。这不同于其他一些使用 MVC 模型的 javascript 框架——它说模型保存数据。我们使用 reducers——redux-
的核心概念——将我们的数据分割成相关的块，以便在应用程序的给定页面中使用。通过这种方式使用数据，我们可以通过固体减速器的设计和重复使用，轻松管理
一组复杂的数据。

一个简单的例子就是上面显示的结果页面。当我们从 API 发出请求时，我们得到一大堆数据。这些
数据的大部分对于结果页面来说是不必要的；然而，我们可以使用 Redux 的 reducers 将数据分割成我们需要的相关的
片段。对数据进行切片是保持每个页面精简的好方法。

## 总之

React 是一个令人惊叹的库，当你将它与正确的工具和概念结合起来时，你可以非常快速地创建非常复杂的应用程序。我们(DRAFT 和我的前端团队)使用一些原则和概念，在非常紧迫的期限内，非常快速地重新创建了整个复杂的 web 应用程序。