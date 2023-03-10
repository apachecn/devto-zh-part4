# 这些生活窍门将改变你写 Markdown 的方式！

> 原文：<https://dev.to/yechielk/these-lifehacks-will-change-the-way-you-write-markdown-3651>

如果你是一名开发人员，你可能以前遇到过 markdown 编辑器(不管你是否知道)。

GitHub、DEV、许多票务追踪器、大多数文档和博客平台都支持 Markdown(咳..咳嗽...中等...).掌握 markdown 语法将有助于您作为开发人员更有效地交流。

有很多资源和备忘单可以帮助你入门(比如[这个伟大的要点](https://github.com/adam-p/markdown-here/wiki/Markdown-Here-Cheatsheet))。在这篇文章中，我将分享一些我没见过太多人使用或者没有很好记录的技巧。

## 有序列表

有序列表的语法非常简单:

```
1\. Item one
2\. Item two
3\. another item 
```

Enter fullscreen mode Exit fullscreen mode

呈现方式:

1.  第一项
2.  第二项
3.  另一个项目

但是你有没有写了一个很长的清单，然后发现你漏掉了中间的一项？回去插入它，然后修改所有后续项目的数字，这一定很烦人...

除了，你不必！Markdown 不关注数字，只要你有一个前面有数字的物品列表，任何数字，它都会渲染成有序列表。

以下:

```
1\. Item one
2\. Item two
3\. another item

1\. Item one
1\. Item two
1\. another item

4\. Item one
6\. Item two
765\. another item 
```

Enter fullscreen mode Exit fullscreen mode

全部渲染为:

1.  第一项
2.  第二项
3.  另一个项目

我喜欢的风格是在它们前面使用 1，但是如果你喜欢有实际的数字，并且发现你自己需要插入一个新的项目，比如在项目 7 和 8 之间，你可以给这个新的项目一个数字 7，当降价被渲染时，它将正确地渲染所有的数字。

## 语法高亮显示

在接下来的部分中，我嵌入了 gist 的代码片段，这是因为 dev.to 编辑器中有一个错误，不能正确地转义反勾号。

*我已经[在 dev.to repo 里开了一个关于它的问题](https://github.com/thepracticaldev/dev.to/issues/3648)。如果有读者想尝试一下，请随意！*

大多数人都知道像这样用三个反勾号包装代码段: