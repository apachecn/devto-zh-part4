# 与 Asciidoctor 和 Hugo 一起写博客

> 原文：<https://dev.to/foodogsquared/blogging-with-asciidoctor-and-hugo-1khc>

最后，我发现 Hugo 和 Asciidoctor 有一个很棒的博客工作流程。

凭借 Hugo 广泛的内置功能集和 Asciidoctor 提供的功能丰富的文本格式选项，它创建了一个由简单的内容管理和有趣的写作体验组成的博客体验。

在我写这篇文章的时候，只有少数[文章](https://rgielen.net/posts/2019/creating-a-blog-with-hugo-and-asciidoctor/)[文章](https://www.bryanklein.com/blog/hugo-asciidoctor-vscode-gitlab-firebase/) [和](https://blog.anoff.io/2019-02-17-hugo-render-asciidoc/) [讨论](http://discuss.asciidoctor.org/Writing-BLOG-in-Asciidoctor-td7015.html)关于用 Hugo 和 asciidor 写博客，所以我会把我的 2 美分加入 Hugo 和 asciidor 的最新版本。把它想象成一份更新报告。

作为将来的参考，以下是本文使用的工具及其版本:

*   Asciidoctor v2.0.10

*   Hugo v0.57.2

| 注意 |
| --- |
| 这不是一个完整的教程，更像是我与 Hugo 和 Asciidoctor 的博客设置的指南。 |

## 先决条件

如果你想看完整篇文章，我假设你已经满足了以下条件:

*   安装了[雨果](https://gohugo.io/)和[阿斯卡里医生](https://asciidoctor.org/)

*   已经了解这两种工具的基础知识

*   已经安装了带有主题的 Hugo 项目

*   **可选**:一个 [Travis CI](https://travis-ci.org/) 账户(或类似 CI/CD 服务)

*   **可选**:一个 [GitHub](https://github.com/) 账户(或类似的远程 Git 回购)

| 小费 |
| --- |
| 如果你不熟悉 Hugo 和 Asciidoctor，它们都有快速入门指南。这是给雨果和 T2 的。 |

## 阿斯基医生和雨果

在包括 Hugo 在内的大多数静态站点生成器中， [Markdown](https://daringfireball.net/projects/markdown/) 在创建帖子方面是唯一的一等公民。然而，在最近的 Hugo 版本中，有一个[外部助手](https://blog.anoff.io/2019-02-17-hugo-render-asciidoc/)功能，它可以调用适当的外部程序来处理特定类型的文件(或文件扩展名)。幸运的是，基于 Asciidoctor 的文件是用 Asciidoctor 自动编译的，所以我们不需要做任何事情。只需安装它，你就迫不及待地要走了。

### 用 Asciidoctor 创建内容

在 Hugo 站点中创建基于 Asciidoctor 的内容非常容易。只需手动创建一个 Asciidoctor 文件，或者您可以使用 Hugo 的方法，这是最佳的方法。

```
hugo new posts/my-first-post.adoc 
```

并且在`content/posts/my-first-post.adoc`应该会有一个新的 Asciidoctor 文件。

最有可能的是，你会看到它的格式像一个 Markdown 文件，因为大多数主题没有焦点。

Hugo 的一个特性是让你为你的日常内容创建[内容模板](https://gohugo.io/content-management/archetypes/#readout)(或者一个原型)。我们用 Asciidoctor 创建内容，所以让我们为此创建一个快速模板。

在`archetypes/default.adoc`中创建一个文件。每当 Hugo 检测到新内容的文件扩展名为`.adoc`时，这将成为主模板。然后，为您的 Asciidoctor 文档创建一个模板。

举个例子，这是我的 Asciidoctor 文档的模板。

```
--- 
title: "{{ replace .Name "-" " " | title }}" 
date: {{ .Date }} //
draft: true

categories:
    - "category1"
tags:
    - "tag1"
    - "tag2"
---

= {{ replace .Name "-" " " | title }}
{{ .Site.Author.name }} {{ with .Site.Author.email }}<{{ . }}>{{ end }} 
{{ dateFormat "2006-01-02" .Date }} 
```

让我们看看模板的不同部分。

*   首要问题。不幸的是，我们仍然必须把这个文档作为 Hugo 识别的内容之一。

*   将文档的辅助文字转换为标题大小写。

*   把作者和邮件一起放在 Asciidoctor 的序言中(如果有的话)。随意丢弃它。

*   ISO 格式的日期。

| 注意 |
| --- |
| 说到 frontmatters，原生 Asciidoctor frontmatter 是不认可的。 |

你可以随心所欲地修改模板。比如用 [MathJax](https://www.mathjax.org/) 写数学公式，可以用[加上词干属性](https://asciidoctor.org/docs/user-manual/#activating-stem-support) ( `:stem:`)。

因为基于 Asciidoctor 的文档只接受基本的支持，所以在对设置满意之前，您仍然需要自己做一些工作。例如，启用语法突出显示和样式化某些东西，如标注、警告块和打开块。

此外，并不是所有的东西都 100%工作，所以你可能会遇到一些问题，这些问题将在后面的文章中讨论。尽管如此，它在很大程度上是有效的，并且您仍然可以使用 Asciidoctor 的更强大的特性集来进行富有表现力的编写。

## 语法高亮显示(不带短码)

语法高亮对于技术博客来说是一个重要的特性，尤其是当你经常需要在文章中展示代码的时候。

在 Asciidoctor 上，您可以使用`:source-highlighter:`属性[启用语法高亮显示](https://asciidoctor.org/docs/user-manual/#enabling-source-highlighting)。你可以在运行时用可执行文件编译它，但是用 Hugo 是不可能的，因为传递给它的参数是硬编码的。然而，您可以为您拥有的每个文档启用它，但是正如您所想象的，它并不理想，并且需要一些手工劳动。

如果你只是依赖 Hugo 的开箱即用的特性(阅读:If)，你可以用 [`highlight`短码](https://gohugo.io/content-management/shortcodes/#highlight)得到它，如果你要迁移到另一个博客平台或静态网站生成器，它会反咬一口。

不过，有一些方法可以在没有 Asciidoctor 属性或 Hugo shortcode 的情况下突出显示 Hugo 站点的语法。只是需要更多的努力才能通过。

启用它的一个更可靠的方法是使用语法高亮器，如 [highlight.js](https://github.com/highlightjs/highlight.js) 或 [PrismJS](https://prismjs.com/) 。我将讨论用 PrismJS 设置它，因为它更容易，这也是我主要在博客上使用的。

作为将来的参考，我使用的 PrismJS 版本是 v1.17.1。

### 获取文件

首先，我们将需要语法荧光笔脚本及其样式表，当然。我建议将文件保存在本地，而不是通过 CDN 链接它们，因为它们通常预先构建了有限的语言和设置支持。

为 PrismJS 获取文件非常容易。

*   进入[下载页面](https://prismjs.com/download.html)。

*   选择缩小版本。

*   选择您认为需要支持的所有语言。

*   包括[“保留标记”](https://prismjs.com/plugins/keep-markup/)插件。

*   下载吧。

如果您使用[asciidor 标注](https://asciidoctor.org/docs/user-manual/#callouts)，您将需要“保留标记”插件，因为 PrismJS 会替换 HTML 元素及其类。

下载完脚本后，将它们放在 Hugo 项目中的某个地方。为此，我假设脚本在`static/js/lib/SYNTAXHIGHLIGHT.js`中。

不要忘记选择一个主题。我假设样式表在`static/css/SYNTAXSTYLESHEET.css`中。

### 与雨果融为一体

现在最困难的部分是，将它们用于你的雨果项目。

在文档主体(`<body>`)标记的末尾之前添加语法 highlighter，并在`<head>`中添加样式表。

它的可用位置取决于主题。我建议开始寻找布局文件夹的默认模板的主题(`theme/$NAME_OF_THEME/layouts/_default`)，然后是部分文件夹(`theme/$NAME_OF_THEME/layouts/partials`)。

| 小费 |
| --- |
| 您可能希望从基础模板(`theme/$NAME_OF_THEME/layouts/_default/baseof.html`)开始。 |

将适当的文件从主题文件夹复制到您自己的布局文件夹，并链接它，类似于下面的代码清单。

```
<!-- Inside of the head element -->
<link rel="stylesheet" href="{{ "css/SYNTAXSTYLESHEET.css" | absURL }}">

<!-- ... -->

<!-- Before the end of the body tag -->
<script src="{{ "js/lib/SYNTAXHIGHLIGHT.js" | absURL }}"></script> 
```

设置完成了！如果你想搬出 Hugo，那就少了一个内容迁移的问题。你会感谢你这样做的。

## 使用工作流的问题

虽然 Hugo 和 Asciidoctor 很棒，但是这个设置也有一些问题。

最明显的是默认后端的 Asciidoctor 的 HTML 输出不是很好，还有很多需要改进的地方。

[![`<div>` then a `<p>` for a paragraph](img/890910a6c8f14747aaf897fe2af2b310.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6bFTPIN0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://foo-dogsquared.github.io/blog/posimg/blogging-with-asciidoctor-and-hugo/asciidoctor-sample-html.png)

图一。`<div>`然后一个`<p>`换一个段落，真的？

它不是语义的，也是非传统的。不仅用 CSS 样式化它是一件痛苦的事情，而且由于它依赖于特定的 HTML 标签结构，还破坏了许多辅助功能，比如屏幕阅读器。

| 注意 |
| --- |
| 您可以通过使用[asciidor html 5s 后端](https://github.com/jirutka/asciidoctor-html5s)来产生正确的、语义 web 友好的输出，从而解决这个问题。记住，从 Hugo 传递给 Asciidoctor 的参数是硬编码的。你必须设法哄骗 Hugo 通过你自己的论点。幸运的是，你不需要担心，因为[已经有人分享了细节](https://ratfactor.com/hugo-adoc-html5s/)。 |

另一个你可能会遇到的问题(也许会让你头疼)是对 Asciidoctor 本身的基本支持，如果你不想太依赖于创建黑客和解决方法的话。

如前所述，Hugo 通过外部助手支持 Asciidoctor。外部助手相对较新，更像是一个实验性的特性。有一个建议是通过添加用户配置来改进它，所以至少这个特别的特性有希望扩展。

还有一个事实是，并非 Hugo 的所有内置特性(如[目录](https://gohugo.io/content-management/toc/))都适用于 Asciidoctor(可能还有其他非 Markdown 格式)内容。幸运的是， [Asciidoctor 本身已经相当广泛了](https://asciidoctor.org/docs/user-manual/)，Hugo 没有太多功能不能用，而且你也不太可能需要它们。

此外，原生 Asciidoctor 前端物质不像前面提到的那样工作。

## 使用 Travis CI 进行部署

[有些](https://rgielen.net/posts/2019/creating-a-dockerized-hugo-asciidoctor-toolchain/) [帖子](https://axdlog.com/2018/using-hugo-and-travis-ci-to-deploy-blog-to-github-pages-automatically/) [是](https://www.martinkaptein.com/blog/hugo-with-travis-ci-on-gh-pages/) [浮动](https://jellis18.github.io/post/2017-12-03-continuous-integration-hugo/) [围绕](https://insileco.github.io/2018/03/30/hugo-github-travis-a-step-in-continuous-deployment/)如何用不同的工具做一个一劳永逸的部署工具链。

就我个人而言，我将我的博客部署到 CI/CD 工作流中。我在工作中使用 Travis CI。

| 注意 |
| --- |
| 你也可以在我的博客的 [GitHub repo 上看看，了解一下它是如何在更大的画面上工作的。](https://github.com/foo-dogsquared/blog) |

下面是我用来部署 Hugo 博客的配置:

```
dist: bionic
language: generic

before_install:
  - sudo apt-get update
  - sudo apt-get install ruby
  # Assuming that the GitHub API is at version 4.0 
  - curl https://api.github.com/repos/gohugoio/hugo/releases/latest | grep "hugo_extended.*deb" | grep "browser_download_url" | cut --delimiter=":" --delimiter="\"" --fields=4 | wget -qi -
  - sudo dpkg -i hugo*.deb
  - sudo gem install asciidoctor 
script:
  - hugo
deploy: 
  local_dir: "public/"
  provider: pages
  skip_cleanup: true
  github_token: $GITHUB_TOKEN
  target_branch: gh-pages
  on:
    branch:
      - demo
      - master 
```

这是配置的明细:

1.  它将使用基于 Linux 的机器，以 Ubuntu Bionic (18.04)作为操作系统。

2.  通过 GitHub release 从其 repo 下载最新的 Hugo 二进制并安装。

3.  安装 Asciidoctor 工具链。

4.  建立 Hugo 网站。

5.  当分支发生在`demo`或`master`时，将构建文件夹部署到我的 GitHub repo 的`gh-pages`分支。

| 注意 |
| --- |
| 只要 GitHub API 版本是第 4 版，配置就应该工作。您可能需要做一些 API 调试和调整来使它正确。 |

根据虚拟主机服务提供商的不同，您可能需要做一些额外的工作，如预压缩文件或配置服务器。由于博客是使用 [GitHub 页面](https://pages.github.com/)托管的，我不需要配置一些东西(对我来说很不幸)。

## 结论

这就是你现在需要知道的所有雨果和阿斯卡里医生的东西。

只要多找些例子，你就会有更多的想法。你可以从我的博客的 GitHub repo 开始。

就个人而言，与 Hugo 和 Asciidoctor 一起写博客很有趣。如此有趣，以至于我最终创建了一个主题，专注于支持 Asciidoctor 内容和其他东西。

虽然并不完美，但与使用 Markdown 进行写作相比，它仍然提供了许多令人满意且更具表现力的写作体验。

尽管这个工作流程有很多不完美的地方，但还是有一些东西值得期待，尤其是 Hugo 的外部助手功能。希望有更多的工具会注意到 Asciidoctor，以及它对于编写技术和基于 web 的内容有多么重要。

## 进一步寻找

### 网页

[*Asciidoctor documentation*](https://asciidoctor.org/docs/)

从官方文档开始使用 Asciidoctor 非常容易。它应该可以帮助你很长一段时间适应它。如果你已经掌握了诀窍，我建议你经常查看用户手册。

[*Better Hugo/AsciiDoc HTML* by **David Gauer** (ratfactor.com)](https://ratfactor.com/hugo-adoc-html5s/)

这是一篇关于如何使用我在文章中提到的 UNIX 路径欺骗技巧使 Asciidoctor 的 HTML 输出比以前更好的简短而有趣的文章。

[*Hugo documentation*](https://gohugo.io/documentation/)

雨果的文档很棒。为新人提供了大量清晰简明的信息，并对内容结构有直观的导航。

### 视频

[*Hugo tutorial series* by **Mike Dane**](https://www.youtube.com/playlist?list=PLLAZ4kZ9dFpOnyRlyS-liKL5ReHDcj4G3)

迈克·戴恩的视频系列。它还作为视频资源出现在 Hugo 官方文档中。视频系列做得很好，提供了简明扼要的解释。

注意:我已经尽力把这篇文章从 Asciidoctor 移植到 Markdown，所以某些东西可能会被忽略。只要看看规范的 URL 就知道我在说什么了。