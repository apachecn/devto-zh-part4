# 宣布 Frontity v1 候选版本+新文档

> 原文：<https://dev.to/frontity/announcing-frontity-v1-release-candidate-new-documentation-42no>

## Frontity v1 发布候选发货

我们非常高兴地宣布，今天我们发布了 Frontity 1.0 的候选版本！

此次发布带来了许多**改进**和 **bug 修复**Frontity 测试版中的问题，稳定了 API，为预计于 6 月 20 日发布的 1.0 版本铺平了道路。

Frontity 是一个开源框架，允许你轻松地为一个无头或分离的 WordPress 站点构建一个 React 前端。点击查看[的介绍文章。](https://frontity.org/blog/frontity-a-react-framework-to-create-wordpress-themes/)

### 改进

*   在火星主题列表中增加了代码分割的例子。
*   在火星主题中增加了 404 页。
*   增加了火星主题列表的分页。
*   现在，每个请求都会克隆包中的状态。
*   现在允许包导出一个返回配置的函数。
*   增加了火星主题的菜单。
*   增加了火星主题的例子。
*   在火星主题中添加了特色媒体组件。
*   增加了对 Webpack 中图像的支持。
*   增加了对火星主题中作者列表的支持。
*   在火星主题中增加了载入组件。
*   增加了对火星主题中 WP align 类的支持。
*   公开 utils 从链接中获取路径和页面，反之亦然。
*   公开 util 以规范化源库中的链接。
*   增加了微型路由器中的`autoFetch`功能，以自动获取每个`actions.router.set`上的数据。

### Bug 修复

*   修正了一些后永久链接配置的错误。
*   修正了生产模式脚本中最后一个斜杠的错误。
*   在巴别塔中使用 compat 模式。
*   支持连接组件中的反作用挂钩。
*   增加了修正以提高灯塔报告中的可访问性分数。
*   改进了对连接组件的类型脚本支持。
*   源现在从链接中删除域，然后将它们保存在状态中。
*   添加了自定义调度程序，以更好地处理连接组件中的异步事件。
*   修正了包含搜索(`?s=...`)的分类 URL 中的一个 bug。

### 突发变化

*   `state.source.data()`就是`now state.source.get()`。
*   开发者需要学习的三个主要 API(`actions.router.set`、`actions.source.fetch`、&、`state.source.get`)现在只接受一个字符串。
*   `state.router.path`现在是`state.router.link`。
*   `state.router.page`没了，用`state.router.link`代替。
*   `state.source.apiUrl`现在是`state.source.api`。
*   如果没有找到`client.js`或`server.js`，入口点现在默认为`index.js`。
*   默认模式现在是“默认”而不是“html”，并且不需要文件夹。
*   列表现在每页都有一个数据条目，而不是所有页面都在同一个数据条目中。
*   `state.frontity.initial`现在是`state.frontity.initialLink`并且是一个字符串(就像`state.router.link`)。

从现在开始没有重大变化的计划。

## 新的和改进的文档

在过去的几周里，我们主要将资源集中在准备发布候选上。然而，我们也花了一些时间来改进文档。我们最近出版的最重要的指南之一是 **Learning Frontity** docs。

[学习 Frontity 文档](https://docs.frontity.org/learning-frontity)解释了框架的所有核心概念(一个接一个),不仅帮助你理解如何开始学习 Frontity，还帮助你理解它是如何工作的。除此之外，我们增加了其他[新向导](https://docs.frontity.org/guides)和这个[部署部分](https://docs.frontity.org/deployment/)。

尽管有这些改进，我们知道文档仍然比理解 Frontity 的概念和 API 所需的要少得多，我们对此表示歉意。

在发布 Frontity 1.0 之前，我们将向 docs 提供更多资源，试图让他们尽快完成任务。

与此同时，请随时在[社区论坛](https://community.frontity.org/c/framework-development/docs-and-tutorials/29)上**开一个新话题**，提出改进建议、修正错误或帮助我们填补文档中的任何空白。

## 帮助测试发布候选

显然，最终的 1.0 版本是我们的重中之重。如果一切按计划进行，我们预计它将于 6 月 20 日发布。

当您开始测试候选版本时，请[报告](https://github.com/frontity/frontity/issues)您发现的任何错误，以便修复它们。对于那些已经测试过测试版的人，我们**建议**你重新开始，[从头开始创建一个新的 Frontity 项目](https://docs.frontity.org/getting-started/quick-start-guide#getting-started-with-frontity)。

如果您有任何问题或反馈，请随时在[社区论坛](https://community.frontity.org/)分享。非常感谢您的意见和帮助。

关于参与 Frontity 的其他方式，请查看[本指南](https://docs.frontity.org/contributing/how-to-contribute)。

## 谢谢！

最后但同样重要的是，非常感谢所有测试 Frontity 测试版、向我们提供反馈、提出问题以及帮助传播 Frontity 消息的人们。特别是那些积极贡献的人:

*   [Marika kn nen](https://twitter.com/koodiela)撰写了关于[如何在 Frontity](https://codeformylife.fi/how-to-add-a-featured-image-to-the-post-component-on-the-frontity-framework/) 的帖子组件中添加特色图片的指南。
*   [Imran Sayed](https://twitter.com/imranhsayed) 制作 Frontity 框架的[介绍视频](https://www.youtube.com/watch?v=P001c2Gg4YM)。
*   [Smit Patadiya](https://twitter.com/smit_patadiya) 创建了关于[如何添加评论支持的指南](https://smitpatadiya.com/2019/05/27/add-post-comment-in-frontity/)。

感谢您的支持和时间！

我们迫不及待地想听听你对这个版本的看法！

*最初发表于[frontity.org/blog](https://frontity.org/blog/announcing-frontity-v1-release-candidate-new-documentation/?utm_source=dev.to&utm_medium=v1-release-candidate-post&utm_campaign=rc-launch)。*