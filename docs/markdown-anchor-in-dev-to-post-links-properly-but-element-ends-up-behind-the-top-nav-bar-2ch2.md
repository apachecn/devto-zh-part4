# 开发中的 Markdown anchor 可以正确发布链接，但元素最终出现在顶部导航栏的后面。

> 原文：<https://dev.to/thegroo/markdown-anchor-in-dev-to-post-links-properly-but-element-ends-up-behind-the-top-nav-bar-2ch2>

在 markdown 中，你可以使用锚来链接文章的特定部分，这在 Dev.to posts 中自然也适用，但当试图使用它时，锚定部分的标题会隐藏在导航栏后面，就像下面的例子:

[锚定到](#relatedposts)下方的部分。

这个标题将会隐藏在导航栏后面

从这里可以看到。

在 dev.to 撰写本文时，对此的最佳解决方案或变通方法是什么？

为了清楚起见，如何复制它的`code`:

创建链接:

```
[Anchor to section below](#anchorname) 
```

Enter fullscreen mode Exit fullscreen mode

联动降价区间:

```
<a name="anchorname"></a> # Normal Markdown Title Here(ends up hidden) 
```

Enter fullscreen mode Exit fullscreen mode