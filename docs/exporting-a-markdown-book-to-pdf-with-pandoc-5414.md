# 使用 Pandoc 将减价书籍导出为 PDF

> 原文：<https://dev.to/thefern/exporting-a-markdown-book-to-pdf-with-pandoc-5414>

一段时间以来，我一直想写一本书。在我的职业生涯中，我在自动化和控制方面积累了很多经验，我自己在软件工程、游戏开发等方面也学到了很多，这个清单是巨大的。

所有这些萦绕在我脑海中的东西都需要在某个时候出来，在我的工作和我的个人项目中，我倾向于很好地记录，这促使我在写作方面变得更好。

我已经决定在 markdown 写几本书，让我们看看第一本怎么样。

## 设置

你用的 markdown 编辑器没什么区别，我用的是 [Typora](https://www.typora.io/) 。如果您不确定 vscode 是否对 markdown 文件有很好的支持，大多数文本编辑器都有。 [@awwsmm](https://dev.to/awwsmm) 在 markdown editors 上写得很好[markdown-editors-2019](https://dev.to/awwsmm/state-of-markdown-editors-2019-2k49)

除此之外，我使用 [Pandoc](https://pandoc.org/) ，你将需要 LaTeX 来打印 pdf，确保遵循[的指示](https://pandoc.org/installing.html)。

## 导出为 PDF

我在目录中组织了我的章节，如下所示:

```
├───title.txt
├───Chapter1
├───Chapter2
└───images 
```

Enter fullscreen mode Exit fullscreen mode

标题就像 pandoc 的页眉:

```
--------
title: Book Example
author: Fernando B.
rights: Nah
language: en-US
-------- 
```

Enter fullscreen mode Exit fullscreen mode

### Pandoc 资源

*   [入门](https://pandoc.org/getting-started.html)
*   [演示](https://pandoc.org/demos.html)

下面的命令将添加目录，输出到 book.pdf，从 title.txt 获取标题信息，并抓取三个 markdown 文件。

```
pandoc --toc -o book.pdf title.txt .\Chapter1\Scene1.md .\Chapter1\Scene2.md .\Chapter2\Scene1.md 
```

Enter fullscreen mode Exit fullscreen mode

## 超越命令行

你可以想象，随着你的书越来越多，事情会变得越来越难编译。我找不到一个库或者一个简单的参数来获取一个目录中的 md 文件列表，所以我写了一个 python 脚本 [export_book.py](https://github.com/kodaman2/markdown-book/blob/master/export_book.py) 。现在脚本需要放在书的根目录下，但是将来我可能会扩展它。

[全额回购](https://github.com/kodaman2/markdown-book)

## 其他要考虑的事项

我需要尝试用 css 来更好地格式化这本书，并添加页眉和页脚等，但对于任何试图完成相同任务甚至撰写学术论文的人来说，这是一个良好的开端，因为 pandoc 可以输出不同的格式。