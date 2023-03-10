# 下载文档离线文档工具

> 原文：<https://dev.to/talk2megooseman/offline-documentation-tools-1jb5>

###### 这是我的第一篇博文...呀！！！

这将是分享我在日常开发中使用的工具、工作流和 IDE 设置的一系列帖子中的第一篇。

今天我想向大家介绍我在 macOS 和 Windows 上使用的离线文档工具。

TLDR:在 macOS 上试试 Dash，在 windows/Linux 上试试 Zeal

## 为什么要使用离线文档工具？

离线文档工具是那些直到你尝试了才知道你需要的工具之一。如果你像我一样，任何一天你都可以用不止一种语言编程(在我的情况下是 Ruby 和 JavaScript)，也许你会使用一个 UI 框架(Bootstrap)，让我们加入一个前端框架(React)。有很多信息需要记住，我有时会忘记 Ruby 和 JavaScript 中有哪些方法。

在最近的情况下，我不记得如何在 Ruby 中对`Array`执行`find`。我知道它在 JavaScript 中叫做`find`,但是对我来说，我不记得在 Ruby 中是否有一个好的、干净的方法来做这件事。

通常情况下，您会执行以下操作:

1.  转到浏览器
2.  在搜索栏中键入`ruby array find`
3.  然后等待结果回来，点击你选择的网站，在那里你可能会找到一些文档

但是使用离线文档工具:

1.  加载应用程序
2.  提取数组的 Ruby 文档
3.  查看可用的数组方法或搜索任何提到 find 的内容

不需要加载任何网页，解析结果，只需直接进入源。

我给大家介绍一下我在 macOS 和 Windows 10 上开发时使用的工具

### macOS

无仪表板(带付费升级)-[https://kapeli.com/dash](https://kapeli.com/dash)

官方描述:

> Dash 是一个 API 文档浏览器和代码片段管理器。Dash 存储代码片段，并立即在离线文档集中搜索 200 多个 API、100 多个备忘单等等。您甚至可以生成自己的文档集或请求包含文档集。

Dash 是一个非常好用的工具，我把它推荐给和我一起工作的每个人。它可以访问您可能使用的几乎所有主要语言/框架/库的文档。额外的好处是，它还有一个很好的备忘单库，可以快速查找如何做某事。Dash 是免费使用的，但带有 nag 模式，要求你购买它，但付费版本带有令人敬畏的集成特性，适用于 [Alfred](https://www.alfredapp.com/) 和大多数代码编辑器/ide。

[![](img/f30b22ad7c0c75b614debd93f62e532d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hjSsj9vx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/idThsbR.png)

这就是我的仪表板的样子。你可以在左边看到许多不同的文档下载，我下载的备忘单藏在折叠下面。

调出文档非常简单，在左上角你可以进行一般性搜索，扫描你下载的所有文档。

[![](img/103ca307f4ca0f8c23bfc63927d60fdf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4qSFljKR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/SGKF34q.png)

你可以在左上角看到所有不同语言/框架/备忘单的`Array find`结果。但是这非常嘈杂，我们知道我们只是想在 Ruby 中搜索一个关于`Array`类的方法。如果您知道 docset 关键字，这很容易做到。在搜索中只需输入`ruby: Array find`

[![](img/0f865a03257224b4148d2ee202637080.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uBDDqWS0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/6cvqccg.png)

我们现在只搜索 Ruby 的结果，然后寻找我们正在寻找的类和/或方法。如果你仔细看左下角的空白处，它还会显示这个类的所有方法，真酷。

大多数文档集已经带有一个定义好的关键字，通常是语言/库/框架的名称。您可以在`Preferences > Docsets`中找到并编辑它们

[![](img/5053e16177b4fee450025179540fb081.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2Za9NqFN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/UDow9cZ.png)

### Windows 10 / Linux / macOS

热心自由-[https://zealdocs.org/](https://zealdocs.org/)

官方描述:

> Zeal 是一个面向软件开发人员的离线文档浏览器。

我最好将热情描述为 Dash 的简化版。它只为大多数流行的语言/库/框架提供离线文档，没有备忘单或代码片段支持。

[![](img/deb42d530dfc622cc7157756e7178d89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n8ZwcQ8G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/x32HDEc.png)

就像使用 Dash 一样，您可以以简单方便的方式搜索和下载文档。

[![](img/7881cda31147429ec1ead6d089b9e2ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m_DEGPRE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/bzpZdQO.png)

搜索文档的功能与 Dash 非常相似，但是它的模糊搜索功能有时会很烦人。

## 总结

使用离线文档工具会非常方便。事实上，在写这篇文章的时候，我正在 Dash 上使用 Markdown cheatsheet。

[![](img/164cb18be17c393e26715321bb6fd281.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ky8HdSCC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/t648cGi.png)

给他们一个尝试，如果你知道另一个工具，请在评论中分享。编码快乐！