# 我们用盖茨比重新创作了 WordPress 的 219 主题

> 原文：<https://dev.to/pagely/we-ve-re-created-wordpress-twenty-nineteen-theme-with-gatsby-2khi>

毫无疑问，WordPress 已经变得如此之大，因为它很容易管理内容。由于可以在 WordPress admin 中进行大量的修改，它使用户能够在没有任何技术知识的情况下进行修改。

伴随着易用性而来的是对开发人员的复杂性和限制。虽然 WordPress 本身对许多网站来说是一个很好的解决方案，但它仍然受到各种细微差别和额外的服务器端处理的影响，这些对其他网站来说可能并不理想。

## 利用 WordPress 的优势

由于 WordPress 最大的优势是在管理内容时的用户友好性，开发者如何利用这些优势，同时仍然利用最适合他们的解决方案呢？当然是通过使用 WordPress 作为他们前端的无头资源！

例如，用 [Gatsby](https://www.gatsbyjs.org) 开发的前端可以使用 WordPress 作为其帖子、页面、类别甚至评论的来源！从用户的角度来看，一切看起来和感觉起来都像 WordPress，但是前端可以静态生成，以利用显著的性能提升和较低的托管成本。

## 我们的解决方案:WordPress + Gatsby

作为一个网站性能和微服务的支持者，我想出了一个解决方案，可以作为如何使用 WordPress 作为用 Gatsby 构建的网站的后端的例子。

[https://Twitter . com/the Jeff matson/status/1161659005208530944](https://twitter.com/TheJeffMatson/status/1161659005208530944)

在这个概念验证示例中，使用原始主题的 CSS 文件重新创建了 Twenty 主题。

你可以在 GitHub 上找到我和 Gatsby 对 2019 主题的重新创作。

## 它是如何工作的

在初始化 Gatsby 构建时，来自 WordPress 源的现有内容(通过 [WPGraphQL](https://www.wpgraphql.com/) WordPress 插件)被自动提取，并使用各种帖子、页面、档案等模板进行构建。

由于 Gatsby 从 WordPress 源代码生成静态页面，这也意味着前端比以前更快更安全。当有人访问您的站点时，不再需要通过 PHP 进行额外的服务器端处理！

### 动态内容呢？

你可能会问自己，“但是动态内容呢，比如评论？”。我们也有同样的想法，并提出了解决方案。

对于动态内容，解决方案是简单地在客户端加载。在注释的情况下，它们既可以通过一个 [GraphQL](https://graphql.org/) API 请求动态加载，也可以在构建站点时静态生成。这两种解决方案都有各自的优缺点，但都提供了额外的灵活性和性能提升。

与选择动态显示注释时如何加载注释类似，注释提交也发生在客户端。走这条路意味着评论可以直接在 WordPress admin 后端进行审核和存储，同时在一个完全不同的位置显示。

## 试驾

如果您想在自己的项目中尝试一下，可以在 GitHub 上克隆或派生[示例库。请注意，这个存储库是作为一个例子提供的**概念验证**和**目前不打算作为生产站点**。](https://github.com/northstack/gatsby-wordpress-twenty-nineteen)

关于如何构建示例项目的更多信息，请看一下[自述文件](https://github.com/northstack/gatsby-wordpress-twenty-nineteen/blob/master/README.md)中的说明。

## 喜欢这个主意吗？以下是你可以提供帮助的方式

我们创建这个例子是为了回馈帮助我们塑造 NorthStack 未来的了不起的 Gatsby 和 WordPress 社区。如果你愿意提供你的专业知识，我们将张开双臂欢迎。

对于 bug 报告或增强功能，请随意在 GitHub 上提交问题或提交请求。

只是想分享你的想法或者你自己对一个使用 WordPress 作为后端资源的 Gatsby 站点的实现？请在下面的评论中告诉我们。

## 额外收获:看看这些其他伟大的开源项目

还有其他开源项目也使用 Gatsby 作为 WordPress 后端的前端，所有的方法都不同。如果你想做同样的事情，看看实现相似最终结果的不同方式可能会有所帮助。

以下是一些其他开源项目，它们使用了带有 WordPress 源代码的 Gatsby 前端:

*   [将 Twenty 主题移植到用于 WP 的 JS 上的 Gatsby](https://javascriptforwp.com/porting-the-twenty-nineteen-wordpress-theme-to-gatsby/)
*   WPGraphQL 团队的示例博客
*   [Gatsby-theme-WP-source-one](https://github.com/progital/gatsby-theme-wp-source-one)由 GitHub 上的 [@progital](https://dev.to/progital)