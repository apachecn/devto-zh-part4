# 回答:导出笔记本到 pdf 没有代码

> 原文：<https://dev.to/espoir/answer-export-notebook-to-pdf-without-code-duplicate-7mm>

让我们分享我最受欢迎的回答！

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [ 回答 re:导出笔记本为 pdf 无代码](https://stackoverflow.com/questions/34818723/export-notebook-to-pdf-without-code/45029786#45029786)

Jul 11 '17[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)74![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/34818723/export-notebook-to-pdf-without-code/45029786#45029786) </header>

我发现[这篇](http://greg-ashton.physics.monash.edu/ipython-nbconvert-latex-template-to-hide-code.html)文章很有趣，它解释了如何删除输入列:

你必须在你要转换的笔记本所在的目录下创建一个名为“hidecode.tplx”的模板文件，并在其中添加这些行:

```
 ((*- extends 'article.tplx' -*))
((* block input_group *))
    ((*- if
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/34818723/export-notebook-to-pdf-without-code/45029786#45029786)</button>