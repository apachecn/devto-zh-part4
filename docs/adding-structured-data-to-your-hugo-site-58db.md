# 向 Hugo 站点添加结构化数据

> 原文：<https://dev.to/pdwarkanath/adding-structured-data-to-your-hugo-site-58db>

如果你在谷歌上搜索一个热门话题，你可以看到一些新闻文章突出显示在谷歌搜索结果的顶部。像这样:

[![](img/79455ea445c76e868ed0e1dcb0b653f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jg_Q8yIM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i5dtg7ldl584gvb12nj0.png)

Google 认为网络上的某个页面是一篇文章，更确切地说是一篇与您搜索的主题相关的文章，因为出版商(如本例中的《印度时报》)已经将结构化数据添加到他们的页面中。结构化数据是一组规则，用来通知搜索引擎你的网站上的特定页面是关于什么的。

向网站添加结构化数据有一些明显的好处。你可以在上面看到，结构化数据的文章看起来不同于普通的搜索结果，即有出版商名称(如印度时报)，缩略图，摘要等。它们比普通的搜索结果占用更多的空间，更重要的是，这些结果被显示在搜索结果的顶部，给了它们最大的可见性。在搜索结果中更高的可见度意味着更多的点击和流量。

如果你有博客，你的文章将会是“文章”或“博客文章”类型。谷歌的网站上提到了一篇文章所需的结构化数据:[https://developers . Google . com/search/docs/data-types/article](https://developers.google.com/search/docs/data-types/article)

完整的规则列表(适用于各种页面)可以在官方模式文档网站上找到:[https://schema.org/docs/full.html](https://schema.org/docs/full.html)

现在，为网站上的每个页面编写结构化数据是很乏味的，尤其是如果每个页面都是关于不同的主题。幸运的是，静态站点生成器允许你创建自己的`partials`，也就是在站点中重复的 HTML 代码片段。通过允许部分变量，有可能为所有页面创建唯一的模式。按照下面的步骤来做。

## 板条箱一个`schema.html`文件

如果您有一个使用 Hugo 构建的静态站点，您需要做的就是创建一个名为`schema.html`的分部。Google 建议您用 JSON-LD 编写结构化数据。不需要太了解 JSON-LD。照原样复制下面的代码片段，你就成功了😛

```
<script type="application/ld+json">
{{ if eq .Section "posts" }}
{
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  "headline": {{ .Title }},
  "image": {{ .Params.featuredImage | absURL }},
  "datePublished": {{ .PublishDate }},
  "dateModified": {{ .Lastmod }},
  "author": {
    "@type": "Person",
    "name": {{ .Site.Params.author }}
  },
  "mainEntityOfPage": { "@type": "WebPage" },
   "publisher": {
    "@type": "Organization",
    "name": {{ .Site.Params.header }},
    "logo": {
      "@type": "ImageObject",
      "url": {{ .Site.Params.logo }}
    }
  },
  "description": {{ .Summary | plainify | safeHTML }},
  "keywords": [{{ range $i, $e := .Params.tags }}{{ if $i }}, {{ end }}{{ $e }}{{ end }}]
}
{{ end }}
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": {{ .Site.Params.header }},
  "url": {{ .Site.BaseURL }},
  "sameAs": [
    "https://www.facebook.com/{{ .Site.Params.facebook }}",
    "https://www.instagram.com/{{ .Site.Params.instagram }}",
    "https://twitter.com/{{ .Site.Params.twitter }}",
    "https://github.com/{{ .Site.Params.github }}"
  ]
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这个文件需要在你的主题的`partials`文件夹中。

## 添加变量值到`config`文件

在您站点的 config.toml 文件中，您需要添加您在上面的`schema.html`文件中使用的变量值。对于我的网站， [WeekInMemes](https://weekinmemes.com) ，我会使用我自己的名字作为作者，社会媒体处理等。对于你的网站来说，用你自己的价值观来取代它们。

```
baseURL = "https://weekinmemes.com"

[params]
author = "DK"
logo = "img/logo.jpg"
header = "Week In Memes"

facebook = "weekinmemes"
twitter = "weekinmemes"
instagram = "weekinmemes"
github = "weekinmemes" 
```

Enter fullscreen mode Exit fullscreen mode

## 添加部分到`head`标签

最后，您需要将这个部分添加到您的`<head>`标签中。通常你的 Hugo 主题会有一个`head.html`片段，你可以在结束`<head>`标签之前添加下面一行:

```
{{ partial schema.html . }} 
```

Enter fullscreen mode Exit fullscreen mode

## 测试

一旦所有这些都准备就绪，您就可以使用常规的构建命令(`hugo -D`或`hugo server -D`)来查看生成的 HTML 文件。

使用 Google 的[结构化数据测试工具](https://search.google.com/structured-data/testing-tool/u/0/)来测试 Google 是否正确读取了所有内容。它应该指出您可能需要纠正的错误(如果有)。

就是这样！