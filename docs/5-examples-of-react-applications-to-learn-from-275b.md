# react 项目 5 个可供学习的 React 应用示例

> 原文：<https://dev.to/gethackteam/5-examples-of-react-applications-to-learn-from-275b>

我第一次开始编程是在 14 岁，当时我正打算建立我的第一家创业公司。我不知道从哪里开始，最终在一个流行的开源电子商务平台 OpenCart 上向前发展。经过许多时间和不眠之夜，我发布了我的初创公司的第一个版本，用户可以在那里交易和出售二手 DVD。几个版本之后，我应用该代码库的所有最佳实践创建了一个定制平台，如果没有任何可以借鉴的例子，我是不可能做到这一点的。

> ![Roy Derks profile image](img/c24ce40630160b984b04c58b70364217.png)

在这篇博文中，我收集了 5 个用 React 构建项目的例子，可以帮助每一个刚开始(React)开发的人提高他们的技能。有时候教程有点太慢或者不够复杂，你只是想探索一个“真正的”代码库。所有列出的项目都有一个定制的后端，或者使用开放 API 来获取数据，所以您不必使用模拟数据。尽情享受吧！

## TMDB 电影数据库

作为一个电影迷，我花了很多时间在 IMDb 上查找节目或人物，但不幸的是，他们没有提供开放的 API。这就是电影数据库(TMDb)出现的原因，它提供了一个伟大的[开源 API](https://www.themoviedb.org/documentation/api?language=en-US) ,提供关于大多数电影和电视节目的信息。这是一个流行的 API，用于(业余爱好)项目或当你真的很喜欢电影时。由[斯蒂芬·肯平](https://twitter.com/s_kempin)开发的这个项目展示了如何在这个 API 之上构建一个电影数据库应用程序，使用 React 和 Twitter 的 typeahead.js 库实现自动建议搜索功能。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[skem pin](https://github.com/SKempin)/[react js-TMD b-app](https://github.com/SKempin/reactjs-tmdb-app)

### Responsive React“电影数据库”(TMDb)应用

<article class="markdown-body entry-content container-lg" itemprop="text">

# imdb 电影搜索

[![License](img/860b06f6cec701708efe1e768851311b.png)](https://github.com/SKempin/reactjs-tmdb-app/blob/master/LICENCE)[![GitHub stars](img/3dae505e33a38945ad01495aa1c46094.png)](https://github.com/SKempin/reactjs-tmdb-app/stargazers)[![GitHub forks](img/cae2ecfdcbf8dc0dd9525c2d1bac095d.png)](https://github.com/SKempin/reactjs-tmdb-app/network)[![Mentioned in Awesome React](img/1ccd181a01eb46221abe3b2cdab927b2.png)T11】](https://github.com/enaqx/awesome-react)

TMDb 电影搜索是一款响应式 [React](http://facebook.github.io/react/index.html) 应用，利用 Twitter 的 [typeahead.js](https://twitter.github.io/typeahead.js/) 和 [Bloodhound](https://github.com/twitter/typeahead.js/blob/master/doc/bloodhound.md) 建议引擎，通过[电影数据库(TMDb) API](https://www.themoviedb.org/documentation/api) 加载数据。

[![](img/5cc49058543deaf7e41a7869ecb36460.png)](https://github.com/SKempin/reactjs-tmdb-app/blob/master/doimg/tmdb-demo.gif)

## 演示

[TMDb 电影搜索-现场演示](https://skempin.github.io/reactjs-tmdb-app/)

## 工具

React 项目中使用的主要工具有:

| 工具 | 描述 |
| :-: | --- |
| [反应](http://facebook.github.io/react/index.html) | 用于构建用户界面的 JavaScript 库 |
| [Typeahead.js](https://twitter.github.io/typeahead.js/) | 一个灵活的 JavaScript 库，为构建健壮的类型头提供了坚实的基础 |
| [猎犬](https://github.com/twitter/typeahead.js/blob/master/doc/bloodhound.md) | Bloodhound 是 typeahead.js 建议引擎 |
| [自举](http://getbootstrap.com/) | 使用世界上最流行的前端组件库，在 web 上构建响应迅速、移动优先的项目 |
| [SASS](http://sass-lang.com/) | Sass 是世界上最成熟、最稳定、最强大的专业级 CSS 扩展语言 |
| [浏览确认](http://browserify.org/) | Browserify 通过捆绑所有的依赖项让你在浏览器中`require('modules')` |
| [巴别塔](https://babeljs.io/) | 立即使用下一代 JavaScript |
| [一饮而尽](http://gulpjs.com/) | Gulp 是一个工具包，用于自动化开发工作流程中痛苦或耗时的任务 |

## 装置

需要 [node.js](http://nodejs.org/download/) 来获取…

</article>

[View on GitHub](https://github.com/SKempin/reactjs-tmdb-app)

## 电子商务首发

在过去的几年里，订阅食物、剃须产品或衣服变得非常流行。有了这个开源产品，你可以创建自己的订阅服务，而且是全栈的！(!！！).使用 Crate，您可以获得一个用 React 创建的前端和一个 Node.js 和 GraphQL 后端。如果你渴望开办自己的公司，并且正在寻找学习如何模块化代码或者集成前端和后端的好材料，那么一定要看看这个资源库。他们甚至使用[故事书](https://storybook.js.org/)，这样你就可以检查这个项目中使用的所有组件。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [阿图尔米](https://github.com/atulmy) / [板条箱](https://github.com/atulmy/crate)

### 👕 👖 📦使用 Node、Express、React、React Native、Redux 和 GraphQL 构建的示例 web 和移动应用程序。非常基本的 stitchfix.com/krate . in 的复制品(允许用户每月订阅时尚服装和配饰)。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Crate](img/2f396d1f8c150efd0f965b416f2b9b0f.png)](https://raw.githubusercontent.com/atulmy/atulmy.github.io/mastimg/crate/hero-with-link.png)

# 板条箱<g-emoji class="g-emoji" alias="shirt" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f455.png">👕</g-emoji> <g-emoji class="g-emoji" alias="jeans" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f456.png">👖</g-emoji> <g-emoji class="g-emoji" alias="package" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4e6.png">📦</g-emoji>

#### 获取时尚服装和配饰的月度订阅。

*   **API** 用 Node、GraphQL、Express、Sequelize (MySQL)和 JWT Auth 构建
*   **WebApp** 使用 React 和 Redux 以及服务器端渲染(SSR) / SEO 友好构建
*   **移动** (Android 和 iOS)本地应用与 React Native 一起构建
*   使用 Babel + Webpack 用 ES6+编写
*   使用 Adobe Experience Design 设计。在这里预览一下[。](https://xd.adobe.com/view/a662a49f-57e7-4ffd-91bd-080b150b0317/)

## 特征

*   模块化且易于扩展的代码结构
*   强调开发者体验
*   UI 组件在单独的文件夹中，可以很容易地替换为您喜欢的 UI 框架
*   React Native 的响应式 UI 支持移动设备和平板电脑
*   带有关联的 GraphQL 模式
*   数据库迁移和数据播种
*   使用 JSON Web 令牌和 GraphQL API 进行用户认证
*   GraphQL 的文件上传功能
*   React storybook 演示了用于 web 的 UI 组件
*   服务器端渲染
*   用于自动化开发体验的多包设置和开发脚本

## 对...有用

*   具备以下基础知识的开发人员…

</article>

[View on GitHub](https://github.com/atulmy/crate)

## 苹果音乐克隆

你在苹果音乐、Spotify 或谷歌上听过音乐吗？这个项目是第一个项目的克隆，甚至附带了一个您可以使用的后端。想要构建自己的后端吗？有自己做那件事的指示。在前端，React 与 Redux 和 Redux Thunk 一起使用，为您提供了一个广泛的示例来开始使用 Redux 进行状态管理。这个项目还不支持 React 钩子，所以把它看作一个挑战，看看你是否能重构它😎。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ tvillarete ](https://github.com/tvillarete) / [苹果-音乐-js](https://github.com/tvillarete/apple-music-js)

### 使用 React & Redux 从头开始构建的音乐流媒体服务

<article class="markdown-body entry-content container-lg" itemprop="text">

## [![og](img/eb59a4fb6629853f176538cbb20c2e7a.png)](https://user-images.githubusercontent.com/21055469/43682115-2b5a1682-981e-11e8-973d-fe316aa3a49b.png)

由坦纳·维拉雷特建造

在 [LinkedIn](https://linkedin.com/in/tvillarete) 上与我联系！我很快就要毕业了；)

# JavaScript 能带我们走多远？

结果是，相当远。这个网络应用是我模仿苹果 iOS 音乐应用的尝试，我想我已经非常接近了！

点击查看现场演示

* * *

## [![screen shot 2018-08-12 at 9 49 19 am](img/92045779df439fac4fe7eac036a0ec20.png)](https://user-images.githubusercontent.com/21055469/44004287-0a541a80-9e15-11e8-93e8-ff3606dd4db1.png)

## [![screen shot 2018-08-12 at 9 49 27 am](img/e8cb7257ab459f0c54ba1913dadfce57.png)](https://user-images.githubusercontent.com/21055469/44004289-0df0907e-9e15-11e8-9bcf-ec5e62bcd70a.png)

我现在大学四年级，看到自己进步了这么多，还在继续提高编程的各个方面，感觉超级爽。

## Backend API

API 托管在一个 Raspberry Pi 上，它是私有的(但是如果你尝试的话仍然可以访问),这样它就不会过载。如果您有兴趣构建自己的后端来插入该工具，下面是我的数据库和端点的样子:

### 数据库ˌ资料库

有六个必需的列:

*   `name`:歌曲的名称
*   `artist`:艺术家姓名
*   `album`:相册名称
*   `track`:那个……

</article>

[View on GitHub](https://github.com/tvillarete/apple-music-js)

## 松弛克隆

如果你是一家公司的开发人员，那么很有可能你一直在使用 Slack 作为交流工具。有什么比克隆一个你每天都在使用的工具更好的学习方法呢？这个由[卢修斯·杰克逊](https://twitter.com/lukejacksonn)开发的 Slack 克隆软件使用 React 和流行产品[聊天工具](https://pusher.com/chatkit)，可以让你轻松创建高级聊天应用。你可以通过申请一个 API 密匙免费开始。发现任何漏洞并愿意开始为开源做贡献？在这个库中有开放的[初级友好](https://github.com/pusher/react-slack-clone/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22)门票。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [卢克杰克松](https://github.com/lukejacksonn) / [反应-松弛-克隆](https://github.com/lukejacksonn/react-slack-clone)

### 完整的聊天应用程序，使用 Chatkit |由 [@lukejacksonn](https://dev.to/lukejacksonn) 构建

<article class="markdown-body entry-content container-lg" itemprop="text">

# 反应松弛克隆

[![Star on GitHub](img/fe65ec3ac10451bd20585aa6f6b10b0f.png)](https://github.com/pusher/react-slack-clone/stargazers)[![Tweet](img/aee66e16254459b91b0aa2aa9128338f.png)](https://twitter.com/intent/tweet?text=Check%20out%20this%20Slack%20clone%20using%20@pusher%20Chatkit%20%F0%9F%91%89https://github.com/pusher/react-slack-clone)[![Build Status](img/9f218fd5042ab4bbbc6a03d1c024c9c8.png)](https://travis-ci.org/pusher/react-slack-clone)

> 由[聊天工具](https://pusher.com/chatkit)支持的 Slack 克隆。在这里可以看到 https://pusher.github.io/react-slack-clone

[![demo](img/cd0babc4636426f0386cc254c392a4e7.png)](https://user-images.githubusercontent.com/1457604/35891289-687ad6ec-0b9b-11e8-99cc-ffbad31a017e.gif)

这是一个静态的单页 web 应用程序，使用 [create-react-app](https://github.com/facebookincubator/create-react-app) 引导，以便于设置、分发和开发。它是一个围绕着 [pusher-chatkit-client](https://github.com/pusher/chatkit-client-js) 库的瘦 UI 包装器，展示了不同的功能如何协同工作，以形成一个具有各种潜在产品应用的引人注目的实时聊天客户端。

## 特征

Chatkit SDK 允许您实现聊天客户端的功能。其中包括:

*   <g-emoji class="g-emoji" alias="memo" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4dd.png">📝</g-emoji>公共和私人聊天室
*   <g-emoji class="g-emoji" alias="satellite" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4e1.png">📡</g-emoji>实时发送和接收消息
*   <g-emoji class="g-emoji" alias="package" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4e6.png">📦</g-emoji>富媒体附件(拖放)
*   <g-emoji class="g-emoji" alias="speech_balloon" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4ac.png">💬</g-emoji>打字和存在指示器
*   <g-emoji class="g-emoji" alias="books" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4da.png">📚</g-emoji>读取消息光标

想参与进来吗？我们有一堆适合初学者的 GitHub 问题。

## 成分

根据此处的文档[，该演示试图使功能完整。功能请求应该通过向此存储库发出问题或请求来进行。](https://docs.pusher.com/chatkit/reference/javascript)

*   创建消息表单…

</article>

[View on GitHub](https://github.com/lukejacksonn/react-slack-clone)

## 黑客新闻克隆

[黑客新闻](https://news.ycombinator.com/)不仅是编程和技术相关新闻的重要来源。它也是开发人员的经典之作，通常是展示新的前端框架或语言的起点。这个由[柯林顿·丹诺尔福](https://twitter.com/clintondannolfo)完成的项目通过在前端使用 React 和 GraphQL，以及在 Node.js 和 GraphQL 上运行的服务器来实现。作为奖励 [Next.js](https://nextjs.org/) 被添加来支持服务器端渲染(SSR)。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [克林顿伍德](https://github.com/clintonwoo)/[hacker news-react-graph QL](https://github.com/clintonwoo/hackernews-react-graphql)

### 黑客新闻克隆用通用 JavaScript 重写，用 React 和 GraphQL。

<article class="markdown-body entry-content container-lg" itemprop="text">

## 黑客新闻克隆 React/GraphQL

[![GitHub Stars](img/dfa752d6fe32cf527d0f930622ab2cf2.png)](https://github.com/clintonwoo/hackernews-react-graphql/stargazers)[![GitHub Followers](img/c24ec06936f915f308ec4ba2738ae585.png)](https://github.com/clintonwoo/hackernews-react-graphql/)[![GitHub Issues](img/5fdf14a891ee7092c7d6b4aadbc1b27d.png)](https://github.com/clintonwoo/hackernews-react-graphql/issues)[![GitHub Pull Requests](img/1202459fac3225759d2d082c000d3046.png)T11】](https://github.com/clintonwoo/hackernews-react-graphql/pulls)

这个项目是用通用 JavaScript 重写的黑客新闻的克隆，使用 React 和 GraphQL。它旨在作为一个示例或样板，帮助您使用生产就绪技术构建项目。

[![Hacker News Clone Demo](img/8df06e079f6e057c9d2c16474e8c4b44.png)](http://www.hnclone.win)

[现场演示](http://www.hnclone.win)

## 概观

### 起重要作用

*   React -(用户界面框架)

*   Web 数据 API(graph QL)

*   阿波罗-(图形 SQL 客户端/服务器)

*   下一步-(路由、SSR、热模块重装、代码分割、构建工具使用 Webpack)

*   TypeScript -(静态类型)

*   web 包(bundler 模块)

*   PostCSS - (CSS 处理)

*   Node.js - (Web 服务器)

*   快速- (Web 应用服务器)

*   护照-(认证)

*   ESLint -(编码最佳实践/代码突出显示)

*   是—(测试)

*   码头工人-(集装箱部署)

*   可选- Yarn 或 Pnpm 软件包管理器-(更好的依赖性)

### 利益

**前端**

*   声明式用户界面- ( `react`)
*   静态打字(`typescript`)
*   GraphQL 片段协同定位- ( `@apollo/client`)
*   预取页面资产- ( `next`)

**服务器**

*   通用 JS - ( `node` & amp…

</article>

[View on GitHub](https://github.com/clintonwoo/hackernews-react-graphql)

你觉得这些项目怎么样？希望他们能帮助你提高反应技能，别忘了留下反馈😄！