# Markdown 是围绕 JAMStack 的标记和其他混淆

> 原文：<https://dev.to/remotesynth/markdown-is-markup-and-other-confusions-around-jamstack-41pg>

我多次从很多人那里听说过 [JAMStack](https://jamstack.org/) 中的 M 被描述为代表[降价](https://daringfireball.net/projects/markdown/)。这是一个很容易犯的错误，因为静态站点生成器已经和 Markdown 紧密联系了很长时间。[例如，Jekyll](https://jekyllrb.com/) 支持 Markdown 作为创建内容页面的默认方式，这可以追溯到它在 2009 年的一些早期版本，如今几乎所有其他主要的静态网站生成器都这么做。所以如果你知道 JAMStack 所基于的工具的历史，认为 M 代表 Markdown 是有道理的。

## M 代表标记

但是，这个 M 实际上是为*标注的* ***上升为*** 。标记是一个更广泛的术语，包括 Markdown，这是一种轻量级的标记形式。一些静态站点生成器工具支持其他类似类型的轻量级标记，包括 [AsciiDoc](http://asciidoc.org/) 和 [LaTeX](https://www.latex-project.org/about/) 。HTML 也是标记(它在 all-Hypertext*Markup*Language 之后的名称中)。

但是 JAMStack 意义上的标记不仅仅是 Markdown 或 HTML。其中的一个关键部分是将这些标记语言与模板结合起来。例如，哲基尔使用[液体](https://shopify.github.io/liquid/)，[雨果](https://gohugo.io/)使用[围棋模板](https://golang.org/pkg/text/template/)，其他许多人使用[手柄](https://handlebarsjs.com/)或类似的东西。这是魔法的一部分，将原本是静态文件的东西变成可以生成整个网站的东西，网站上充满了可以作为 HTML、CSS 和 JavaScript 部署的内容。

## M 为更

这就是 JAMStack 中的 M 超越了你可以使用的 Markdown、HTML、YAML 和 Liquid 的地方。几乎任何网站都包含 JavaScript、API 和标记，但并不是所有这些网站都是用 JAMStack 构建的。M 包括将这些放在一起的构建过程——这意味着 M 包括您的静态站点生成器。如果这在名字中隐藏了一点，那也不是完全无意的。要理解为什么，您需要稍微回顾一下这种 web 开发方法的历史。

JAMStack 这个术语基本上是由 [Netlify](https://www.netlify.com/) 联合其他一些公司在 2016 年左右开始为这种方法开发工具的[创造的。我已经写了很多书和文章，并广泛地介绍了这个主题，当时有人找我谈过这个问题，老实说，我当时并不喜欢这个名字，但我理解它背后的原因，而且老实说，我没有任何更好的建议。](https://web.archive.org/web/20160603092304/http://jamstack.org/)

问题是，到那时为止，我们一直简单地称它们为静态站点，它们是用静态站点生成器构建的。这并没有反映现实，即这些网站已经变得越来越动态。今天的 JAMStack 站点通常与任何其他包含动态内容、身份验证等内容的动态 web 应用程序没有什么区别。称它们为静态网站是不公平的，也是误导的。所以是 JAMStack 但是让我们强调一下 M！