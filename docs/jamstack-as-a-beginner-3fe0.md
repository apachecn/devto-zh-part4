# 作为初学者的 JAMstack

> 原文：<https://dev.to/len/jamstack-as-a-beginner-3fe0>

我最近建立了自己的网站和博客。这是我第一次从零开始从事一个合适的 web 开发项目，我想从下面的搜索历史中我们都可以同意我基本上什么都不知道(我们都必须从某个地方开始，对吗？).

[![i1](img/1b20c618dcdc0957cbce14c4ce5de07b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--750Rp9dA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tmgba6re40jyaf6imook.png)

[![i2](img/bb60f70a3fe9ac6283dcf4ce41f8c5c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XXkGUt_x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m83axor2nbdalg765l5b.png)

[![i3](img/f0fa944c8edd3001785982bfd7f123d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TW4jbEEp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ubhh8d1uq8v7sd5ef0lr.png)

基本上，我知道像 WordPress 和 Squarespace 这样的大型服务器端内容管理系统，它们处理从 UI 到后端代码的所有事情，我还知道 LAMPstack(只是不知道它的名字)(稍后会详细介绍)。我不知道的是 JAMstack——而 jam stack 正是我要找的。

### 那么，是什么呢？

JAMstack 是一个建立快速、安全和静态网站的好方法。如果你从未听说过 jam stack——下面是它的内容:

(请记住，并不是每个 JAMstack 网站都有 JAM 的所有元素——它们共有的核心特征是它们都是无服务器的)

#### J 为 JavaScript

JavaScript 负责将所有数据提取到您的网站(以及在客户端完成的任何其他动态编程)。JavaScript 也可以是您想要的任何风格！反应？Vue.js？.......香草 JS？？

#### A 为 API

这个 API 就是你访问数据的方式。通常在你的服务器上的东西现在被抽象为一个 API。在大多数情况下，您会将您的数据放在一个被称为 headless CMS 的第三方服务上(如 Contentful、Directus、Kentico Cloud 等)。

#### M 代表标记

标记(不是降价)有两种常见的表现形式，即 HTML 和 XML。“不是所有的网站都有标记吗？是什么让标记成为 JAMstack 如此重要的一部分？”你可能会问。事情是这样的:与动态网站不同，动态网站会为收到的每个请求创建一个新的 HTML 页面(这涉及到查询数据库并将结果与标记和插件相结合)，JAMstack 页面是在部署时使用静态站点生成器(Jekyll、Gatsby、Hugo)预先构建的。这意味着他们应要求以最小的延迟时间得到服务。这不仅使它们更快，而且没有数据库查询也使它们更安全。

### 为什么要用？

有很多文章谈论 JAMstack 为什么好，最后归结为 4 点:使用这个架构构建的网站更快、更安全、可伸缩性更好，并且是模块化的——带来更好的开发体验。

对我来说，我选择 JAMstack 是因为我想要一种简单的方式来编辑网站内容，同时尽可能保持网站的安全和简单。

### 如何使用 JAMstack 制作网站？

在我看来，用 JAMstack 构建一个网站比 LAMPstack(整个服务器端工具)更容易上手。我只花了几天时间就把我的网站部署到了万维网上——在我开始之前，我对 JAMstack 一无所知(CSS 也占用了 90%的时间)。

以下是您可以在中使用 JAMstack 的一些配置:

1.  我的配置:Gatsby JS(静态站点生成器)+ Contentful(无头 CMS) + Netlify(以分布式方式托管站点的 CDN)
2.  Jekyll (SSG) + Github 页面(用于托管)
3.  中间人(SSG) + S3(简单仓储服务)+ Netlify

这里有一堆 SSG:[2018 年前 10 名 SSG](https://www.netlify.com/blog/2018/08/24/the-top-10-ssgs-of-2018-according-to-staticgen-and-github/)
这里有一堆无头 CMS: [10 个无头 CMS 要考虑](https://geekflare.com/headless-cms/)
这里有一堆静态网站 cdn:[最好的静态网站托管提供商是什么？](https://www.slant.co/topics/2256/~best-static-website-hosting-provider)

### 教程？延伸阅读？

绝对是！我用的教程是[内容管理与盖茨比，心满意足& Netlify](https://itnext.io/content-management-with-gatsby-netlify-and-contentful-70f03de41602) 。

另一个优秀的 JAMstack 教程是[如何用 Jekyll 和 Make 制作一个便宜、快速、简单的博客](https://www.explainhownow.com/2018/starting-a-jekyll-blog/)

在学习 JAMstack 和相关问题时，我发现文章[jam stack 的甜蜜](https://thenewstack.io/the-sweetness-of-jamstack-javascript-apis-and-markup/)特别有帮助，写得很好。当然，还有 jamstack.org 的。

### 结束

感谢阅读！如果你喜欢，请查看我的推特 [@len_coded](https://twitter.com/len_coded) 或者我的(新鲜制作的，JAMstack)网站【www.lencoded.com T2】。