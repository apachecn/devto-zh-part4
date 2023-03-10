# 全新的每日故事书

> 原文：<https://dev.to/dailydotdev/brand-new-storybook-for-daily-155d>

作为我们正在进行的开源和每日记录之旅的一部分，我们现在推出了一个故事书应用程序来演示所有的日常组件。

让我知道你的想法:)

[https://storybook.dailynow.co](https://storybook.dailynow.co)

源代码在这里:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ dailydotdev ](https://github.com/dailydotdev) / [日常应用](https://github.com/dailydotdev/daily-apps)

### 你每天看到的一切👀

<article class="markdown-body entry-content container-lg" itemprop="text">

# 日常应用

**Everything you see on Daily <g-emoji class="g-emoji" alias="eyes" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f440.png">👀</g-emoji>**

[![Build Status](img/6e14e88164fdd41bccb4e1f70ecfdc7f.png)](https://circleci.com/gh/dailydotdev/daily-apps)[![License](img/7d190da5bcb218b085ec541ac149940c.png)](https://github.com/dailydotdev/daily-apps/blob/master/LICENSE)[![StackShare](img/07740945224ecc23676e2d9e5ea29b7d.png)](https://stackshare.io/daily/daily)[![Storybook](img/2ea3e39bab9d2a6bf4efa27e45c00938.png)T11】](https://storybook.daily.dev)

这是一个 monorepo 与大多数日常的前端项目。它是作为 Daily 2.0 refactor 的一部分创建的，用于将关注点分离到不同的独立项目中。重构的主要概念是让其他人也能在他们的应用程序中使用日常风格指南。

## 技术

*   用于管理依赖性的纱线。
*   节点 v10.21.0(为 [nvm](https://github.com/nvm-sh/nvm) 用户呈现一个`.nvmrc`)。
*   用于管理单一回购的 lerna 。

## 项目

### 成分

实现每日风格指南的库，它主要包含 Vue 通用组件(如模态、文本框和开关)，但也包含定义色板、排版等更多信息的样式表[点击此处](https://github.com/dailydotdev/daily-apps/tree/master/packages/components)。

### 服务

Typescript 库，用于与 Daily 的后端服务进行交互。该库将 http 请求封装为简单的方法，并定义了相关的类型，以便于使用。更多信息[点击此处](https://github.com/dailydotdev/daily-apps/tree/master/packages/services)。

### 延长

每日…

</article>

[View on GitHub](https://github.com/dailydotdev/daily-apps)