# 在 Markdown 中编写 HTML

> 原文：<https://dev.to/smonff/writing-html-in-html-20mk>

这是我今天早上在 [WebOps Weekly](https://webopsweekly.com) 时事通讯上看到的关于 CMS 和站点生成器的[有趣博客文章](http://john.ankarstrom.se/html/)的快速评论。

这完全有意义:要么你可以用像 [Seamonkey](https://www.seamonkey-project.org/) 这样的所见即所得编辑器编写，要么用 Markdown 编写，然后用 [Pandoc](https://pandoc.org/getting-started.html) 用 oneliner 转换成 HTML:

```
pandoc 2019-06-27.md -f markdown -t html -s -o 2019-06-27.html 
```

Enter fullscreen mode Exit fullscreen mode

然后，把整件事扔到你的网站上。