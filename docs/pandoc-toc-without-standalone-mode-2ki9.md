# 无独立模式的 Pandoc 目录

> 原文：<https://dev.to/bachmeil/pandoc-toc-without-standalone-mode-2ki9>

这是一个很少有人会觉得有用的专业岗位。但是因为它不是你可以通过搜索轻易找到的，所以我会把它提供给任何处于我位置的人。

我想在 Pandoc 的文档中添加一个目录，但是没有独立模式——这是不可能的。然而，你可以使用一个非常简单的模板，叫做 blank.html:

```
$toc$
$body$ 
```

就是这样。然后这个

```
pandoc -s file.md -o file.html --template=blank.html 
```

和不使用独立模式一样好。