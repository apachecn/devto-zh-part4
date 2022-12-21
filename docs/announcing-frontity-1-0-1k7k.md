# 宣布 Frontity 1.0！

> 原文：<https://dev.to/frontity/announcing-frontity-1-0-1k7k>

今天，我们非常兴奋地向全世界介绍 Frontity 1.0！🎉

这对我们 Frontity 来说是一个重要的里程碑，对你们所有人来说也是如此，你们从一开始就对这个项目感兴趣，并以许多不同的方式提供支持。

## WordPress + React 立易

Frontity 是 WordPress 的一个免费的开源 React 框架。它允许你很容易地为一个无头或解耦的 WordPress 站点构建一个 **React 前端**，它通过 WordPress REST API 提供数据。

结合 WordPress 和 React 有很多优点，但是开发者需要学习和配置很多东西。与其他 React 框架不同，Frontity 是一个固执己见的框架 **100%专注于 WordPress** ，旨在使一切更简单，即使对于不熟悉 React 的开发人员也是如此。要了解更多，请查看[文档](https://docs.frontity.org/)或这个[的介绍文章](https://frontity.org/blog/frontity-a-react-framework-to-create-wordpress-themes/)。

## Frontity 1.0 的新增功能

与此版本相关的是，API 是稳定的，并且**没有重大变化**被包括在内。下面是我们所做的特性和改进的总结。

#### 特性

*   Html2React 的发布(贡献者: [orballo](https://github.com/orballo) )。谢谢大家！)
*   添加 URL 重定向(贡献者: [DAreRodz](https://github.com/DAreRodz) )。谢谢！)
*   添加对安装在子目录中的 WP 的支持(贡献者: [DAreRodz](https://github.com/DAreRodz) )
*   添加支持将页面作为主页，将博客放在其他地方(贡献者: [DAreRodz](https://github.com/DAreRodz)
*   添加设置以更改类别和/或标签基本 URL(贡献者: [DAreRodz](https://github.com/DAreRodz) )
*   添加带有延迟加载的图像组件，包括对即将到来的本机延迟加载的支持！(投稿人: [orballo](https://github.com/orballo) )
*   添加钩子以了解组件是否在屏幕内(contributor: [orballo](https://github.com/orballo) )
*   **100 灯塔评分** : Frontity 经过优化，在灯塔中获得最高分，包括性能、SEO 和可访问性。主题开发者从 100/100 开始，他们只需要在给主题添加特性时维护它。
*   完美的可访问性:作为我们让用 WordPress 和 React 建立网站变得更容易的使命的一部分，我们也想开发专注于这方面的框架。默认情况下 Frontity 是可访问的，如果开发者破坏了它，它会提供工具让开发者知道。

#### 改进

*   运行 frontity dev 时自动打开浏览器(投稿人: [luisherranz](https://github.com/luisherranz) )。谢谢大家！)
*   从 REST API ( [DAreRodz](https://github.com/DAreRodz) )检索的链接中删除 WordPress 路径
*   将 CSS 文件作为原始字符串导入，以便与([Luis heranz](https://github.com/luisherranz))一起使用
*   将 HMR 添加到 Frontity 状态( [luisherranz](https://github.com/luisherranz) )
*   添加对 WordPress ( [orballo](https://github.com/orballo) )延迟加载插件的支持

#### Bug 修复

*   修复在本地使用 https 模式时导入证书的问题
*   使 isFetching 和 isReady 属性始终存在( [DAreRodz](https://github.com/DAreRodz) )
*   用 es5 捆绑包修复几个错误

## 开始建设令人惊叹的网站！

Frontity 真的很容易上手。

*   **[快速入门指南](https://docs.frontity.org/getting-started/quick-start-guide)** 将带你从最基础的东西开始，感受 Frontity 带给你的惊喜！
*   为了保持 Frontity 及其软件包的更新，您也可以遵循[本指南](https://docs.frontity.org/guides/keep-frontity-updated)。

一如既往，请随时在[社区论坛](https://community.frontity.org/)分享您的反馈和问题。

Bug 报告和其他类型的**贡献**也受到高度赞赏。实际上，除了提交之外，还有很多方法可以帮助项目。查看[这份指南](https://docs.frontity.org/contributing/how-to-contribute)了解更多信息。

我们很高兴听到您对 Frontity 1.0 的看法，并看到您构建了什么！

[https://www.youtube.com/embed/FCnGfYfWulA](https://www.youtube.com/embed/FCnGfYfWulA)

## 接下来是什么

我们将继续开发 Frontity，关闭 bug 并添加我们路线图中的功能。此外，我们还有这两个目标:

*   **文档**:通过添加新的内容和指南，使其更加清晰，并对其进行改进。
*   **Demos** :构建不同的例子和项目来演示如何使用 Frontity 完成特定的任务。

如果你已经用 Frontity 建立了一些东西，请[与社区分享](https://community.frontity.org/c/community/showcases/19)来帮助和激励他人。

## 谢谢

我代表 Frontity 的所有团队，向所有测试过测试版和候选发布版、给过我们反馈、回应过问题、提交过拉取请求、传播过框架的消息，或者在让我们走到这一步的过程中起过作用的人致以深深的谢意。

特别感谢他们的贡献、支持和帮助:

[Luis Herranz](https://github.com/luisherranz) 、[【David arenas】](https://github.com/DAreRodz)、[【Eduardo campaign】](https://github.com/orballo)、[【Carlos Bravo】](https://github.com/c4rl0sbr4v0)、[奥斯卡表](https://github.com/cluny85)、 [Smit Patadiya】](https://github.com/smit-patadiya) [【乔丹克里斯蒂】](https://github.com/jordanchristie)、[【Christopher hyne】](https://github.com/chyne)、[【janak 坎儿基会】](https://github.com/janakkaneriya007)、[【Anatoly dov gun】](https://github.com/adovgun)、[【constantin】](https://github.com/Neugierdsnase/)、【Ucan】、 [Philip Ingram](https://github.com/pingram3541)

来吧，试试 Frontity 1.0，开始构建一些很棒的东西吧！我们认为你会喜欢它的。💙

##### 附加资源

*   [Frontity Docs](https://docs.frontity.org/)
*   [核心概念](https://docs.frontity.org/learning-frontity)
*   [导轨](https://docs.frontity.org/guides)
*   [GitHub 库](https://github.com/frontity/frontity)
*   [社区论坛](https://community.frontity.org/)

*最初发表于[https://frontity.org/blog/](https://frontity.org/blog/announcing-frontity-1-0/?utm_source=dev.to&utm_medium=v1-release-post&utm_campaign=v1-launch)。*