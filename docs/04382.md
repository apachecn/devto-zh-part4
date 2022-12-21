# 有趣的项目:星际飞船技术支持(Angular / NgRx)

> 原文：<https://dev.to/integerman/fun-project-starship-tech-support-angular-ngrx-1913>

这是一篇关于 Angular 应用程序的短文，我早期构建这个应用程序是为了好玩，也是为了让我过去工作中的 Angular 技能保持最新。

我计划定期发布关于特定技术课程的更新，所以我想在此之前分享它的早期状态。

### 理工大

在技术方面，我使用 Angular 8 和 NgRx 来实现基于 Redux 的状态和 Bootstrap 4 样式。这是我第一次使用 Redux state，这是我的主要学习目标。我已经写了一些关于这个的早期想法。

[![integerman](img/c4617a5228f5ea62d27f47eb980b349d.png)](/integerman) [## 早期关于角坐标的思考

### 马特伊兰 1919 年 9 月 1 日 4 分钟阅读

#angular #typescript #devjournal](/integerman/early-thoughts-on-angular-with-redux-ngrx-kj3)

我还没有使用外部 API 或任何类型的数据存储机制。那是以后的事。

### 这是什么东西？

至于这个项目*是什么*，那就更难解释了。想象一下一个基于网络浏览器的工作模拟游戏，其中你管理一个 IT 故障排除/支持团队——为你的员工处理优先级和任务，鼓励他们成长，保持他们的士气，等等。

现在想象这发生在一艘星舰上，你的员工基本上是维持星舰运行的人。

它模糊不清，难以解释，但它是一个有趣的角度开发主题的沙盒。

最终，我想象你管理一个小团队，然后承担更多的责任，比如两个团队，然后是一个部门，然后是整个星舰。

很难说它最终会是什么，因为我会以我觉得最有趣和/或最有趣的方式来理解这个概念。

### 这个东西是什么状态？

我在晚上尽我所能地工作，这是非常早期的，甚至不是一个游戏。

*   没有赢的条件
*   没有失败的条件
*   没有办法和你的员工互动
*   不会创建新票证
*   没有人解决任何问题

所以，是的，这是一个非常早期的占位符，但修补起来很有趣，随着时间的推移，我应该会找机会分享一些代码。

### 我能看看吗？

这是开源的，有 GitHub 源代码。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [英特尔](https://github.com/IntegerMan) / [星舰技术支持](https://github.com/IntegerMan/Starship-Tech-Support)

### 基于计算机用户界面的管理模拟游戏。在西班牙！

<article class="markdown-body entry-content container-lg" itemprop="text">

# 星际飞船技术支持

## 概观

基于计算机用户界面的管理模拟游戏。在西班牙！

## 技术细节

### 平台

Angular 8 带有用于状态管理的 TypeScript 和 NgRx

可能依赖于用 NodeJS 编写的后端 web 服务器

### 目标

这针对的是一种基于网络的交付机制，具有移动或桌面(通过电子)支持的可能性。

这将被部署到 Itch.io 以及我选择的第三方网站。

### 设备考虑因素

这将需要支持 IE 11，Chrome，Firefox，Safari 和 Opera。

## 游戏部分

### 基于回合的

这是一个回合制管理游戏，玩家可以在点击“开始轮班”之前做出决定。一旦一个班次开始，它将完成，你会看到时间卡和每个人在你的团队所做的更新

### 工作项目

船员将有各种工作项目要做，包括

### 船员

### 玩家互动

玩家将进行各种互动:

*   查看…的结果

</article>

[View on GitHub](https://github.com/IntegerMan/Starship-Tech-Support)

此外，您可以通过位于[https://integerman.github.io/Starship-Tech-Support/](https://integerman.github.io/Starship-Tech-Support/)的 GitHub 页面在浏览器中查看最新版本