# Rel Noopener 什么是 rel="noopener "？

> 原文：<https://dev.to/gulshansaini/what-is-rel-noopener-1214>

> 原[帖](https://tutorial.tips/what-is-rel-noopener/)

在 HTML 中，链接类型表示两个文档之间的关系，其中一个使用`<a>`、`<area>`、`<form>`或`<link>`元素链接到另一个。

当您使用`target="_blank"`打开另一个页面时，该页面可能会在与您的页面相同的进程上运行，除非启用了站点隔离。另一个页面可以通过`window.opener`属性访问您的`window`对象。这使得其他页面将你的页面重定向到一个恶意的 URL——参见[关于 rel=noopener](https://mathiasbynens.github.io/rel-noopener/) 了解更多细节。然而，正如 [@Jake](https://twitter.com/jaffathecake) 在他的[文章](https://jakearchibald.com/2016/performance-benefits-of-rel-noopener/)中提到的，web 的原始安全模型阻止其他页面读取您的页面。

## 解

包括所有外部链接的`rel="noopener"`，通常在使用`target="_blank"`时。新窗口将在单独的进程中运行，它将无法访问父页面的`window.opener`属性。你也可以使用`rel="noreferrer"`来防止`Referer`标题被发送到新页面。

## 自己试试

*   [谷歌](https://www.google.com)——既不是`noopener`也不是`noreferrer`

```
<a target="_blank" href="https://www.google.com">Google</a> 
```

Enter fullscreen mode Exit fullscreen mode

*   [谷歌](https://www.google.com) - `rel="noopener"`

```
<a target="_blank" rel="noopener" href="https://www.google.com">Google</a> 
```

Enter fullscreen mode Exit fullscreen mode

*   [谷歌](https://www.google.com) - `rel="noreferrer"`

```
<a target="_blank" rel="noreferrer" href="https://www.google.com">Google</a> 
```

Enter fullscreen mode Exit fullscreen mode

**验证步骤**

1.  点击链接并转到新页面/选项卡
2.  打开开发人员控制台，检查`window.opener`的值
3.  当你在开发者控制台中时，进入网络选项卡并重新加载页面以检查`Request Headers` (Chrome 浏览器)下的`referer`。对于第一个&，第二个应该是`https://gulshansaini.dev/what-is-rel-noopener/`，而在第三种情况下，`referer`属性不应该出现。

## 如果我们使用 noopener 或 noreferrer，SEO 会受到影响吗？

搜索引擎不考虑 noopener 或 noreferrer 对页面进行排名。它用于提高网站的安全性。

## 益智药的好处

*   提高安全性
*   改进的性能——如果新的标签/页面运行昂贵的 JavaScript，它不会影响引用的页面，因为它不会在单独的进程上运行。

原文链接:[https://gulshansaini.dev/what-is-rel-noopener/](https://gulshansaini.dev/what-is-rel-noopener/)