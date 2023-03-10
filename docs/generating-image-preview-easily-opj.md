# 轻松生成图像预览

> 原文：<https://dev.to/suntong/generating-image-preview-easily-opj>

### 背景

很长一段时间以来，`geany`只提供他们主题的截图，而不是全部的预览。我知道我可以[浏览它们](https://github.com/geany/geany-themes/tree/master/screenshots)并逐一了解每个主题的样子，但这是我想完全避免的事情。

使用 [`easgen`](https://github.com/go-easygen/easygen) ，这是如此琐碎，预览可以在几秒钟内完成。(关于`easgen`的更多信息请见本博客末尾)

### TL'DR

这就是我所做的，以及你如何复制它。

*   抓取一份 [Images.tmpl](https://gist.github.com/suntong/3a31faf8129d3d7a380122d5a6d48ff6#file-images-tmpl) 放在某个地方。
*   在`geany/geany-themes`文件夹中，执行以下操作:

```
ls | sed 's/^/  - /; 1s/^/Images:\n/;' | tee /tmp/Images.yaml 
easygen ~+1/Images.tmpl /tmp/Images.yaml | tee README.md 
```

Enter fullscreen mode Exit fullscreen mode

就这样，超级简单，我们完成了。图像预览库已经准备好了(当然是在您之后)。

在撰写本文时，我的公关还没有被接受，所以你可以通过浏览/比较这里的和这里的很容易看出区别。公关现在采取和叉删除。

关于我公关的整个故事，有一个关于使用 JS 或一些服务器端语言即时创建主题画廊的讨论。然而，这需要额外的资源(托管它的公共站点)和额外的时间(在编码这样的前端/后端实现中)。我并不反对服务器端的实现，只是想展示一个简单的解决方案，不需要任何额外的*。*

 *### 定制

好了，下面是执行上述两个命令时实际发生的情况:*