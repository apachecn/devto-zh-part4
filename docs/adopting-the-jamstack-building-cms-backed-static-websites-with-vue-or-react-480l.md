# 采用 JAMStack:用 Vue 或 React 构建 CMS 支持的静态网站

> 原文：<https://dev.to/codegram/adopting-the-jamstack-building-cms-backed-static-websites-with-vue-or-react-480l>

*最初发表于 [Codegram 的博客](https://www.codegram.com/blog/a-jamstack-journey-headless-content-management-with-vue-react)T3】*

> **注意:** *这篇文章的标题也可以叫做*忘了 Wordpress 和 Drupal* 吧，但是经过一点思考，我认为这不是一个公平的比较，因为它们也是非常好的工具。但在某种程度上，它会是。好吧，继续读下去找出原因！*

你好！因此，如果你在这里，你可能已经听说过 **JAMStack** ，或者你对它很好奇。*凉凉凉凉*。我也是——希望这篇文章能为你节省一些时间，如果你想开始做的话。

## 什么是 JAMStack？

JAMStack 这个词几年前才开始被使用。它代表 *JavaScript、API、标记*，正如他们在[官网](https://jamstack.org/)上所说。它不是关于特定的语言或技术，而是一个运行时&部署链的抽象定义。

这个术语是由 Netlify 创造的——但是它背后的工具 T2 概念已经存在很长时间了。事实证明，当它们放在一起时，会产生很大的冲击力:

*   **静态生成的**站点
*   使用最新的 **JavaScript 框架** &库
*   利用**API**(REST、GraphQL 或其他任何东西)
*   部署到 **CDN**
*   **原子**部署
*   **通过 WebHooks 实现自动化**部署
*   玩**无服务器**真的很好

它提供的好处非常多:

*   **无与伦比的首字节时间**:由于我们将使用 CDN 来服务我们的整个请求周期，浏览器试图获取我们的首字节信息所花费的时间仅受 CDN 的限制。
*   **零时间部署:**没有与部署相关的引导时间，这意味着新的部署或回滚是即时的。
*   **安全性:**受攻击的范围大大减少，因为我们的大部分请求现在都由久经考验的 cdn 提供服务，这些 cdn 与我们的 API 没有直接联系。
*   更简单的模式:在一个被 Docker、Kubernetes、Buildpacks &这样的公司主宰的世界里，JAMStack 是一股清风。
*   **简单的可伸缩性:**如果你的网站在运行时不使用 API(网站完全是静态构建的)，你可以肯定你的网站不会因为流量激增而瘫痪。
*   **将表示与数据分离**:通过将数据源与实际应用程序分离，我们获得了更多的技术选择。虽然这对许多水疗中心来说是真的，但走 JAMStack 的路会迫使你，这就是 RealGood™️.

## 无疑，无疑。该不该用？

和大多数技术一样，JAMStack 并不适合所有人。但是，如果您正在构建:

*   一个大部分是公开的网站，由服务器端的 CMS 支持(比如 WordPress，Drupal，Joomla)...).
*   一个**单页**应用程序
*   任何一种典型的**单片应用程序**，其读取远远多于写入。

它可能有其他用例，但目前这些是我觉得自己使用它最舒服的场景。我真的很喜欢它。

一个很好的用例是[粉碎杂志](https://www.smashingmagazine.com/):他们[在 JAMStack](https://www.smashingmagazine.com/2017/03/a-little-surprise-is-waiting-for-you-here/) 上运行！

## 开发者体验是什么样子的？

我之前说过 **JAMStack** 的一大卖点是开发者体验。下面是如果你*接受 JAMStack 的话，一个正常的开发日会是什么样子:*

1.  你用你最喜欢的技术来写你的网站: **Vue，React** ，或者甚至是普通的**HTML/JS/CSS**——在实践中，你会用到一些我稍后会谈到的构建工具。你把它的渲染绑定到一些外部 API 的数据上，也就是一个**无头 CMS** (我稍后也会谈到！)
2.  你**将你的网站**推送给你最喜欢的 **Git** 提供商:GitHub、BitBucket 等。
3.  在您的部署过程中，您的网站从您的 API 中获取所有需要的数据，并静态构建，生成一堆 HTML、JS & CSS。
4.  你的网站被发布到一个 **CDN**
5.  用户访问你的网站，并从始至终得到静态数据，所以你不必担心扩展你的 APIs 数据嵌入在你的标记中。
6.  当你的数据源上的内容发生变化时，一个 **webhook** 就会被触发，重建并重新部署整个网站。

你可能已经看到这一切是如何与自动化工具如 [GitHub actions](https://github.com/features/actions) 甚至 [Zapier](https://zapier.com) 完美结合的。巨大的胜利！

## 售出。今天怎么开始用？

我确信我还缺少很多工具和服务，但是这里有一个我曾经使用过的工具和服务的汇编，我真的可以推荐！

### Web 应用框架

构建支持 JAMStack 的站点的第一步是选择我们的框架。

#### 盖茨比

盖茨比是支持 **JAMStack** 的最雄心勃勃的项目之一。它使用 React 构建，允许将几乎任何类型的数据源嵌入到内部 GraphQL 数据库中，供您查询。所有被查询的数据都将被静态地构建到模板中，这使得它非常快！

根据我的经验，使用他们的 GraphQL api 可能会有点麻烦，而且在某种程度上受到限制，特别是在涉及关系数据或其他复杂查询时，但是我的经验有限，而且有点过时——这可能在过去几个月有所改变。也就是说，这是一个伟大的项目，具有突出的创新，如自动响应图像、开箱即用的 PWA 支持等等。

#### 网格体

[Gridsome](https://gridsome.org/) 是新人，Vue equivalent(*equivualent*？)敬盖茨比。仍处于早期阶段，它看起来很有前途。

虽然它缺乏盖茨比所拥有的更大的生态系统，但它吸取了它们的最新功能并加以改进，我个人预计它会发展得相当快。他们的文档一天比一天好。

我们用它为 2019 年的 [Full Stack Fest 建立了网站，它非常棒。看看它有多快！](https://2019.fullstackfest.com)

#### Nuxt.js

在用 **Vue.js** 构建 web 应用时，Nuxt 是主导框架。受 **Next.js** (其在 React 世界中的对应物)的启发，它提供了一种静态生成模式，将页面中使用的数据嵌入到 HTML 模板中。

只有一个问题:对原始数据 API 的调用仍然会在页面转换时发生。根据我们最近的[推特交流](https://twitter.com/josepjaume/status/1093473712588894209)，看起来他们正在[努力](https://github.com/nuxt/rfcs/issues/22)并且他们意识到了他们的局限性。

Nuxt 对 i18n 的支持很大，有一个大的生态系统。值得去看看！

### 无头 CMSs

让我大开眼界的是，我发现我绝对可以将 CMS 委托给外部提供商。虽然有些用例需要定制的后端，但大多数用例都非常简单，可以通过 UI 进行配置。没有人喜欢一遍又一遍地重新发明轮子！

它们有很多，但我将只描述我在生产中实际尝试过的那些。

#### 心满意足

毫无疑问，Contentful 是最大的玩家，最近获得了*3350 万美元*的融资，所以它不会去任何地方。

它的后端对于大多数用例来说非常简单和灵活，包括对 I18n 字段、内容关系、丰富的 HTML 内容编辑器等的支持。然而，它出乎意料地昂贵。不过，他们有一个免费的计划，可能适用于大多数用例。

他们的数据是通过 REST API 公开的，但是他们有一个关于更大计划的 GraphQL。

#### 棱镜

[Prismic](https://prismic.io/) 拥有出色的 UI 和用户体验。它支持 I18n 文档、关联、响应图像和许多种类的字段。他们也有用于主要 JS 框架的工具，一个很棒的查询 API，等等。他们正在[开发一个 GraphQL API](https://prismic.io/blog/graphql-api-alpha-release) ，有望很快向公众发布！

我和他们一起工作的经验非常丰富，到目前为止，他们的响应时间总是**稳定**和**可靠**(这是当你把你的内容委托给外部平台时你要寻找的核心东西之一)。值得一试！

### 部署平台

我确信部署静态内容有很多选择，但我只列出我能推荐的:

#### Netlify

[Netlify](https://www.netlify.com/) 它对 **JAMStack** 来说再合适不过了——值得注意的是，他们的首席执行官提出了这个术语——所以期望它支持他们需要的一切甚至更多，包括:

*   缓存的自动构建
*   部署预览，与 GitHub 的 PRs 同步
*   A/B 测试
*   支持无服务器功能

我使用 Netlify 的体验从一开始就很棒。我说过他们对个人计划是免费的吗？

不，我没有为他们工作，也没有任何协议。我就是喜欢。

#### 您选择的 CI+S3+【CloudFront | cloud flare |。*]

因为最终你只是部署静态站点，使用你自己的**CI**lint&构建然后部署到 AWS S3 或类似的**是完全可以的**。你只需要写管道的一部分；回滚、部署预览等。

**我的建议**:如果你想利用你自己的 CDN，就用 **Netlify** 来支持它👌

* * *

[![](img/06d89b9efe2ea8009f454a3f71d025ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XlYqBOJh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ua613dwhoti9kakz1id2.gif) 
*给你一些时间休息，让它沉下去*

## 我在使用 JAMStack 时学到的东西

### 每一次变化后的重建都完全没问题

如果你的网站本质上不是非常动态的，那么在每次修改后重新构建是完全没问题的:你提前**消耗 CPU 时间**来换取节省你的服务器在运行时处理 html 字符串所花费的**时间。这不是一个很好的权衡吗？毕竟，我们不是已经在 CIs 上投入了数千小时吗？举个例子，我们有一个 **webhook** ，每次发布播客剧集时，它都会重建我们的 [Full Stack Fest 2019](https://2019.fullstackfest.com) 网站。在处理客户项目时，我们对它进行了设置，这样每当一篇新闻文章发表时，整个网站都会重新构建(这种情况每天都会发生几次)。**

### 还可以提供高度动态的内容

如果你的一些内容是高度动态的，这没什么不好——你总是可以在运行时使用外部 API。例如，注释就是这种情况。Gridsome，Nuxt & co 允许您将组件树的一部分提交给客户端。这对 UX 来说非常好——用户可以尽快获得服务器端的内容，而页面的其余部分可以异步加载。这也适用于管理面板，私人用户区和更多！

### 很高兴你的 HTML 又回来了

让你的应用程序归结为一堆静态文件有点像是在提醒你网络在 GoodOldDays™️.是怎样的这使得新的可能性成为可能，如**渐进式网络应用**，发布到 **P2P 网络**(参见[烧杯](https://beakerbrowser.com/)和 DAT&)更多！

### 有助于 SEO

虽然有时是一种限制，但是不能根据客户端的头动态地改变所提供的内容(参见 I18n)会迫使您走上一条真正有助于 SEO 的道路。这一点，以及内容以惊人的速度提供的事实，加起来就是一个快乐的谷歌机器人。

## 因为妈妈

> 唷，那真是一场脑残！

如果您渴望了解更多关于 JAMStack 的信息，这里有一些很好的资源可供参考:

*   [Full Stack Fest 2019](https://2019.fullstackfest.com/) 会议(我们恰好在 [Codegram](https://codegram.com) 举办)的特点是 **Brian Douglas** ，他是 JAMStack 的原始支持者之一，也是 [JAMStack 广播播客](https://www.heavybit.com/library/podcasts/jamstack-radio/)的主持人。嘣，不要脸的塞！
*   JAMStack 简讯:Netlify 策划的关于 JAMStack 的简讯。
*   [Awesome JAMStack](https://github.com/automata/awesome-jamstack) :继 *awesome-* * repo 系列之后的资源汇编。
*   **其他资源**:[jam stack 网站](https://jamstack.org/resources/)提供其他资源，如关于它的演讲和文章。

感谢您阅读本文，我希望它能帮助您开始进入 **JAMStack** 世界！

> *想找人帮你在**JAMStack* *上构建下一个项目？请在[hello@codegram.com](//mailto:hello@codegram.com)给我们打电话，我们很乐意帮助你！*