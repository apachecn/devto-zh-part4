# 从 WordPress 到 JAMStack

> 原文：<https://dev.to/dennisview/from-wordpress-to-jamstack-1ob4>

[![](img/53ecc18a009fb39625463e0975c78e06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B0v6snJ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A1-C77YdcsonHe7CJ.png)

远离 WordPress，走向拥有 Eleventy 和 Netlify 的 JAMStack。多种语言，一个域名的移动…八月让一切焕然一新。

新的设计，新的技术，更快的加载性能——我的个人网站的新版本终于上线了🎉

这是一个很好的机会来展示后台正在发生的事情，我打算在未来对这个网站做些什么，并给你自己的博客提些建议。

### 多语制&域名转移

第一个变化:我的博客现在可以通过 d-hagemeier.com 访问，而不是 d-hagemeier.de。这一步的主要原因是未来所有内容的双语化。每篇文章都将在未来以德语和英语发表(我是如何在技术上实现这一点的，将很快在一篇单独的文章中发表)。

同时，内容也很陈旧。2016-岁。基本上是我在其他网站上发表的文章的存档。缺少的是写更多文章的纪律。

营销，网页开发和设计将是未来的重点，我个人的目标是每个月至少写一篇文章。也许，会有一两篇超越这些话题的文章——谁知道呢？😉

### 这是一场比赛:第十一场&比赛

从技术上来说，最大的一步是对第十一名和第三名[的改变。多年来，我在 WordPress 上建立了我所有的专业和私人项目，这是我的 CMS 首选。但是随着 5.0 版本的发布，WordPress 在我看来有些臃肿。是时候来点新鲜的了。](https://www.netlify.com/)

我听说过 JAMStack ( **J** avaScript， **A** PIs 和 **M** arkup)，但入门相当困难。基本思想是一种高性能、易于管理的网站的新方法。JAMStack 不使用 PHP 和 WordPress 之类的数据库，而是生成可以“无服务器”交付的 HTML 文件。

听起来是静态的？除了生成器的名字(静态站点生成器，简称 SSG)，它根本不是静态的。为了使编程尽可能灵活，SSG 主要依赖于 Liquid 或 Nunjucks 等模板语言。变量、过滤器或循环在构建过程中被解析和转换。

其他任务可以通过 JavaScript 和 API 的连接来解决。这使得在不依赖基于服务器的编程语言的情况下解决复杂的订购过程成为可能，例如在线商店的订购过程。

伟大的理论，然而入门对我来说很难。我习惯了 PHP，很难摆弄主要 SSG 的给定结构，比如 [Jekyll](https://jekyllrb.com/) 。

当我发现扎克·莱泽曼斯的第十一部分时，这一切都改变了。基于 NodeJS，您可以在结构上获得最大的灵活性，几乎可以想象到每一种模板语言，一个包含大量教程和初学者项目的详细文档…这是一个再好不过的开始。

### 如何使用我的网站？

我所有的文章都是以减价文件的形式写的。标题、出版日期或 SEO 信息等附加信息出现在文章的标题中。简化后，一篇文章看起来像这样:

Eleventy 没有指定这些降价文件位于哪个目录中。同时，我使用 Nunjucks 创建模板。如果你第一次打开一个 Nunjucks 文件，代码看起来像 HTML。最后，它没有什么神奇之处——Nunjucks 只是函数和变量的扩展。

每种内容类型的基本布局非常简单:

使用 include I 加载附加组件，在本例中是页眉和页脚区域。头部只包含文档类型、元标签或样式表的链接(类似于 WordPress 中的 header.php)。Nunjucks 很酷的一点是:通过使用 markdown 文件头的变量，之后生成的所有 HTML 都可以动态调整。例如，标签看起来像这样:

对于本例，这只会导致:

对了，我只拿了我网站老版本的一篇文章，手动复制的。如果我在 WordPress 中已经有了大量的文章，我会使用[WordPress Export to Markdown](https://www.npmjs.com/package/wordpress-export-to-markdown)从 WP 导出文件生成 Markdown 文件。

这个新网站版本的另一个变化是:所有源代码都可以在 Github 上公开获得。所以如果你想更仔细地看看这个结构，可以随意查看一下[库](https://github.com/dennishagemeier/d-hagemeier)！

虽然被称为“无服务器”，但还是需要服务器的。所以我需要一个招待。

到目前为止，我的私人博客可以在 uberspace 的网络空间上访问。对于一个“经典”的网站，我可能永远不会改变——支持是世界上最好的，性能优于大多数超级跑车，整个商业模式是基于“支付你想要的”。

但是后来，网络生活出现了。所以我做出了选择。

任何问“为什么”的人都应该试试 Netlify。不到三分钟，我的网站就上线了——Netlify 只需要指定一个存储库，然后下载所有需要的包，执行定义的构建过程，并直接在. netlify.com 子域下提供活动目录。

额外的噱头简化了很多，像优化 CSS 或图像文件，优化网址或按语言创建动态重定向。

一旦 Github 目录中的内容发生变化，页面就会被重新构建。或者您可以使用 webhooks 并手动触发部署(例如，对于主页上的我的推文)。

### ToDos

和往常一样，我的待办事项清单上还有很多。

目前该网站不包含任何类别的网页。多亏了 Eleventy 中的标签，定制的归档页面非常容易构建(在我的例子中，所有文章都已经被划分到匹配的集合中)。

我也想尝试网络提及。这是来自 IndieWeb 的一个协议，通过它，像评论、喜欢或转发这样的信息可以以一种标准化的方式传输。多亏了像 [Bridgy](https://brid.gy/) 这样的工具，你甚至可以从 Twitter 或 Instagram 导入数据。

我的计划是:执行[Max bcks](https://mxb.dev/blog/using-webmentions-on-static-sites/)伟大的指令，并在文章下方显示对类似文章的所有评论。

此外，我正在研究自动生成 OG 图像，SVG 集成还不是最佳的…你注意到了，我还有一些事情要做😄

如果您发现任何错误，请给我您的反馈或写一条短信。我还没写完这个博客😛

*原载于 2019 年 8 月 20 日*[*【https://www.d-hagemeier.com】*](https://www.d-hagemeier.com/en/articles/wordpress-to-jamstack/)*。*