# 盖茨比 vs Next。JS -什么，为什么，什么时候？

> 原文：<https://dev.to/jameesy/gatsby-vs-next-js-what-why-and-when-4al5>

在我写完这篇文章将近两年后，仍然有人在读它——这太棒了！但不幸的是，这篇文章中的许多内容已经过时了。

我写了这篇文章的新版本，更新到 2021 年。可以在这里找到-【https://www.jame.es/gatsby-vs-next-js-2021】

* * *

好吧，首先，我跑题了，我是这两个“框架”的忠实粉丝。我通常会在我的 Twitter 或 Instagram 上滔滔不绝地谈论它们，然而，毫无疑问，在谈论这些工具时，我被问得最多的问题是哪个更好？

*我该不该用 Next。JS？但是我听说盖茨比很漂亮🔥，也许我应该用那个？*

所以我想我应该更深入地讨论一下，希望能让这个选择更清晰一点。

让我们战斗吧！

[![fight!](img/1e29e11f654d6d44dea14b88d77c66d2.png)](https://i.giphy.com/media/1RzxeL2PuHYD1pw32i/giphy.gif)

* * *

## 盖茨比&下一篇-简介

那么《盖茨比》和《Next》除了你之前听过提到但从未真正理解的流行语之外，还有什么呢？

用最基本的术语来说，同理，`create-react-app`会为你创建一个 React 项目的样板，这两个框架会为你创建一个应用奠定基础。

然而，它们已经从`create-react-app`中分离出来，在某种意义上，它们不被归类为样板，而是工具包，奠定基础，然后给你一套如何建造房子的指导。

总结一下:

**`create-react-app`——奠定 React 项目的基础。剩下的就看你的了。**

接下来——为 React 项目打下基础。给你一些指导方针，告诉你如何在它们的基础上进行构建。

...

但是呢？这很奇怪吗？他们都是...一样的东西？

算是吧。

乍一看，它们在某种意义上看起来非常相似:

*   提供一个样板应用程序。
*   生成令人难以置信的高性能，易于访问和搜索引擎友好的网站。
*   创建开箱即用的单页应用程序。
*   拥有非常棒的开发者体验。

但实际上，它们是根本不同的。

* * *

## 服务器端渲染 vs 静态生成

[![huh?](img/3514e60b18741ddde8392c89482a1a5b.png)](https://i.giphy.com/media/APqEbxBsVlkWSuFpth/giphy.gif)

好了，我们开始变得有点技术化了，所以听我说...还不算太差！

**Gatsby** 是一个静态站点生成器工具。静态站点生成器在构建时生成静态 HTML。它不使用服务器。

**接下来。JS** 主要是服务器端渲染页面的工具。每次使用服务器收到新请求时，它都会动态生成 HTML。

当然，两者都可以调用 API 客户端。最根本的区别是 Next 要求服务器能够运行。盖茨比完全可以不需要任何服务器。

Gatsby 只是在`build time`生成纯 HTML/CSS/JS，而 Next 在`run time`创建 HTML/CSS/JS。所以每次有新的请求进来，它都会从服务器创建一个新的 HTML 页面。

*我不打算在这里深入讨论每一个的利弊，但是为了更深入的阅读，请查看这篇文章-[https://dev . to/stereo booster/server-side-rendering-or-SSR-when-it-for-and-when-use-it-2cpg](https://dev.to/stereobooster/server-side-rendering-or-ssr-what-is-it-for-and-when-to-use-it-2cpg)*

* * *

## 数据处理

这两种工具的另一个根本区别是它们处理数据的方式。

*Gatsby 告诉你应该如何处理应用程序中的数据。*

下一步让你自己做决定。

[![what?](img/f2648773066ca4dbff63df3d0a028454.png)](https://i.giphy.com/media/yoJC2ybvWveeDpddVS/giphy.gif)

那到底是什么意思？

Gatsby 用了一个叫 GraphQL 的东西。GraphQL 是一种查询语言，如果你熟悉 SQL，它的工作方式非常相似。使用一种特殊的语法，你描述你想要在你的组件中的数据，然后这些数据被提供给你。

当组件需要时，Gatsby 使这些数据在浏览器中可用。

一个例子

```
import React from "react"
import { graphql } from "gatsby"
export default ({ data }) => (
  <div>
    <h1>About {data.site.siteMetadata.title}</h1>
    <p>We're a very cool website you should return to often.</p>
  </div>
)
export const query = graphql`
  query {
    site {
      siteMetadata {
        title
      }
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，您可以看到我们有一个获取`title`的查询，然后在组件中显示`title`。厉害！

Gatsby 也有许多针对各种数据源的插件，这(理论上)使得针对许多数据源的集成变得容易。数据源插件的一些例子有 Contentful、Wordpress、MongoDB 和 Forestry。这允许你做一些事情，比如把你的网站挂在 CMS 上，并对内容进行外部控制。

在为生产而构建时，不再使用 GraphQL，而是将数据持久化到 JSON 文件中。

...好酷。

另一方面，如何管理数据完全取决于你自己。您必须根据自己的架构来决定如何管理数据。

这样做的好处是，你不会被任何你可能想或不想使用的技术束缚住。

* * *

## 那么我该怎么选择呢？

你是否应该使用 Gatsby 或 Next 很大程度上取决于你的用例，因为它们真的都很棒。

#### 下次什么时候用。射流研究…

如果你有大量的内容，或者你希望你的内容随着时间的推移增长很多，那么静态生成的网页就不是你的最佳解决方案。原因是，如果你有大量的内容，建立网站需要很多时间。

当创建一个有数千个页面的非常大的应用程序时，重新构建会相当慢。如果你点击发布后必须等待一段时间，这不是一个完美的解决方案。

因此，如果你有一个网站的内容，你会希望随着时间的推移和增长，然后下一步。JS 是你最好的选择。

此外，如果您希望在如何访问数据方面有更多的自由，接下来。JS 值得一喊。

这里值得一提的是，Next 的文档是我见过的最好的文档之一。它有一个互动的介绍，当你浏览内容时会测验你，以确保你能跟上:)太棒了！👏

#### 什么时候用盖茨比

当我建立小规模的网站和博客时，我倾向于使用 Gatsby，这是我个人的偏好。这个生态系统非常适合挂在 CMS 上(这简直是轻而易举的事情),而且有一些很棒的指南教你如何使用它。

(在我看来)和盖茨比一起生活更容易，所以这一点值得记住。同样，文档是一个非常高的水平，充满了教程供您跟随。

Gatsby 还附带了一些“入门”模板，以及一个相对较新引入的“主题”,所有这些都使一个功能完整的 web 应用程序快速启动并运行。