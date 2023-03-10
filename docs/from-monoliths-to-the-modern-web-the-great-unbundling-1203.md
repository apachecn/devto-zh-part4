# 从巨石到现代网络——伟大的拆分

> 原文：<https://dev.to/stackbit/from-monoliths-to-the-modern-web-the-great-unbundling-1203>

近年来，从使用 Wordpress 等单一项目构建网站到使用站点生成器、Headless CMS 和部署工作流等更现代的方法的转变正在加速。我写这篇文章是因为我发现自己一遍又一遍地讲述着同样的故事，我想写下我自己的观察和想法，并与你分享。

**史前史**

在过去的好时光里，我们可能会在共享 VPS 上使用 Wordpress 这样的发布工具，生活很简单，我们利用 WP/cPanel 这样的操作系统，只需支付很少甚至没有支付任何费用就可以访问商品化的主机。虽然这在一段时间内是有意义的，但它很快成为运行一个网站的次等方式，主要原因是速度(根据每个请求渲染页面)和安全性(WP 每隔一天就会被黑客攻击，有时一天两次)。虽然我们可以完全控制我们的内容是如何发布和呈现的，但感觉我们牺牲了阅读、写作和托管体验的重要部分。

我记得有一天，我厌倦了运行一个不断被黑的缓慢的 WP 网站，决定把我网站的静态快照放在 S3 上。事情立刻变得安全、快速和简单...至少在到了编辑我的网站的时候，我意识到我无意中或者有目的地放弃了使用 CMS 来编辑和创建内容。有一段时间，我用 Cyberduck 来编辑我网站的 HTML/CSS，这是一个 Mac FTP 客户端，也支持在 S3 桶中编辑内容。没有所见即所得，没有降价，没有预览，没有拖放图片上传，但这种简单的体验引起了我的共鸣。这有点像带着数量有限的设备露营，可以让一个人重新思考他们回家后的日常设置。

这让我开始思考如何编辑内容，并产生了一个基于 JS 的 CMS 原型，它可以直接与 S3 交互，并存在于你的浏览器中。你在网站上编辑一个页面所需要的一切都在那个页面里，一旦你点击保存，就会被写到 S3。这种情况持续的时间比你想象的要长，这让我很好奇在单一出版系统之外编辑网站会发生什么。

**分离内容编辑和呈现**

生态系统中随之而来的是旧的孤岛式系统的概念崩溃，以及公司/产品的出现，这些公司/产品使得编辑内容变得容易，而不太考虑如何以及在哪里使用该内容。你可能听说过像 [Contentful](https://www.contentful.com/) 、 [Sanity.io](https://www.sanity.io/) 、 [DatoCMS](https://www.datocms.com/) 等公司提供基于 API 的 CMS，或者 [Forestry.io](http://forestry.io) 和 [NetlifyCMS](https://www.netlifycms.org/) 提供基于 git 的 CMS。他们都专注于内容创建和编辑体验，而不是内容实际上是如何进入网络、移动设备等的。当与静态站点渲染和部署解决方案(如 [Netlify](https://www.netlify.com/) )结合使用时，这种分离会带来卓越的安全性，因为主要攻击媒介(大型 OSS 项目中的漏洞以及对软件运行位置的完全可见性)会立即消失。换句话说，Contentful 可能比 WP 更安全，但更重要的是，攻击者不太可能知道你甚至用它来管理你的内容。

**基于 git 的管道的出现**

大约在这个时候，前端开发框架的发展开始快速加速，伴随着这种复杂性而来的是建立前端管道的需求，或者说是潜力。网站开始由像哲基尔、T2、雨果以及后来的 T4、盖茨比和 T5 这样的软件生成，内容在构建时从无数的数据源中提取。这是用额外的构建工具和捆绑器如 Gulp、Webpack 等包装的。幸运的是，像 Netlify 这样的公司建立了平台来帮助我们在更高的层次上管理和思考这些过程。

[![oyetoket image](img/2b9c360f105ef29d22a9ddaa7dfcb8ee.png)](/oyetoket) [## 哪个是最好的静态站点生成器，为什么？

### oye toke Toby May 30 ' 191 分钟阅读

#go #javascript #discuss #webdev](/oyetoket/which-is-the-best-static-site-generator-and-why-42e2)

git 和 Github 在这一重组中扮演的角色至关重要，因为你的网站已经从一个安装了 WordPress 的 VPS 实例变成了一个带有 webhooks 的 git repo，变成了 CI/CD 服务。这个 git repo 包含您选择的生成器的配置、模板，有时甚至是 Markdown 格式的网站内容。当使用基于 API 的 CMS 时，您的 repo 将包含从您选择的 CMS 中获取最新版本内容所需的所有配置。

JAMstack 和现代网络

你把这个过程和从另一边出来的网站叫做什么？不久前，Matt Biilman 和 Chris Bach 创造了术语 [JAMstack](https://jamstack.org/) ,用来描述从 LAMP 等旧的特定技术堆栈到更现代的架构的转变，在这种架构中，可以从客户端 JavaScript、可重用 API 和预建标记构建网站。JAMstack 这一术语的迅速采用提醒我们，有时清楚地阐明一种已经被许多人好奇或玩弄的方法，有助于迅速加快这种方法和术语的采用。

[![borisschapira image](img/d34455bc0b25521d289dcc416ad0ce4e.png)](/borisschapira) [## 用 JAMStack 回到静态:实现更好的 UX 和 web 性能的范式转变

### 鲍里斯·夏皮拉 2 月 23 日 1810 分钟阅读

#webperf #static #webdev](/borisschapira/back-to-static-a-paradigm-shift-for-better-ux-and-web-performance-4ljc)

**利用这些新方法的力量**

一旦我们认识到内容可以在一个地方轻松创建，并在一个或多个其他地方使用，这将开启许多新的有趣的工作流。值得注意的是，历史上使用 RSS 提要将内容从 web 上的一个地方同步和聚合到另一个地方，这可以被视为互连不同内容源和发布系统的需要和愿望的有力标志。唯一改变的是工具(webhooks、CMS APIs、GraphQL)和新的工作流——它们支持更精细的工作流。

你的站点不再是用相同的代码库或者甚至是在相同的服务器上被编辑和呈现。解开巨石意味着我们使用更合适的软件和方法来解决创建现代网站中存在的每一个挑战。我们现在处于 2019 年，感觉我们正处于一个独特的时间点，我相信现代堆栈已经被许多前沿开发人员/商店所采用，但与此同时，围绕此堆栈的工具仍处于起步阶段。

去年，当我意识到这一点时，我和几个朋友坐下来试着设计一些想法，试图将我们习惯的消费者网站建设者的简单性与现代建筑和生态系统的力量结合起来。这些原型迅速进化，并达到了我们都同意有一个大的有价值的问题可以在这里解决的地步。我们称我们的公司为[stack bit](https://www.stackbit.com/),因为我们喜欢软件堆栈和位，这也有助于该领域的可用性。通过 Stackbit，我们希望让每个人都能轻松地创建现代网站，我们相信，像 Stackbit 和其他人这样的想法正在推动整个类别的发展，因为它们通过公平的竞争环境增加了采用率。

如果你想了解更多关于现代网络生态系统的信息，这里有一些链接

*   [https://www.stackbit.com](https://www.stackbit.com)
*   [https://jamstack.org](https://jamstack.org)
*   [“为什么静态站点发电机是下一个大事件”](https://www.smashingmagazine.com/2015/11/modern-static-website-generators-next-big-thing/)，马特·比尔曼
*   [“交付现代网站体验:内容网格之旅”](https://www.gatsbyjs.org/blog/2018-10-04-journey-to-the-content-mesh/)，Sam Bhagwat