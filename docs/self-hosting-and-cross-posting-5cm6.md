# 自托管和交叉发布

> 原文：<https://dev.to/schuchard/self-hosting-and-cross-posting-5cm6>

# 所有权 vs 触角

当涉及到为个人博客或网站制作内容时，常常会有所有权和覆盖面之间的争论。我敢打赌，大多数人没有一个成熟的在线平台，获得数千或数百万的页面浏览量，并希望获得更好的搜索引擎排名。这往往迫使内容创作者在自我托管和拥有自己的内容之间做出选择，或者在像 [Medium](https://www.medium.com) 或 [dev.to](https://www.dev.to) 这样的成熟平台上发布，以所有权为代价获得更多读者。

## 交叉过账救场

有一个概念叫做交叉发布，你在你的网站和其他领域发布内容。起初，你可能会简单地复制一篇文章的内容，并在另一个平台上创建一个重复的帖子。然而，这不会给你任何好处，当涉及到搜索引擎排名。要发布“重复”的内容，您需要使用规范的链接。

## 规范链接

交叉发帖很有价值，但前提是你能够将重复的帖子与规范的链接联系起来。一个[规范的](https://en.wikipedia.org/wiki/Canonical_link_element) [链接](https://developer.mozilla.org/en-US/docs/Web/HTML/Link_types)允许搜索引擎

> 通过指定网页的“标准”或“首选”版本，防止搜索引擎优化中的重复内容问题。

这告诉搜索引擎，在索引期间，你的文章可能包含重复的内容，但原始的权威来源可以在规范的链接网址找到。这一般是你在 Medium 这样的网站上交叉发帖时的网站。

## 重复内容的危害

没有一个规范的链接，搜索引擎被迫决定哪篇文章是真实和最终权威的唯一来源。鉴于 Medium 等平台上的内容数量庞大且排名靠前，在没有规范链接的情况下发布重复内容可能会侵蚀网站的搜索引擎排名。这可能会导致你的网站排名下降，重复的文章或者更糟的是，你的网站可能会从谷歌索引中完全删除，在这种情况下，它将不再出现在搜索结果中。

## 拥有并分享您的内容

幸运的是，通过使用规范链接，你可以拥有自己的内容并交叉发布以获得最大的影响力。通过首先在你的网站上发布，你将有一个通过规范链接指向原始内容的 URL。

### 中等规范链接

Medium 支持规范链接，但是**只通过他们的导入工具支持**。要使用它，您需要向导入工具提供原始文章的 URL。然后，它会将内容解析成一篇草稿文章。您可以通过检查 HTML 源代码、找到`<head>`标签并搜索`rel="canonical"` `<link>`来验证这一点。你可以在我的网站上用这篇指向最初的[的](https://kevinschuchard.com/blog/2018-11-20-schematic-sandbox/)[文章](https://medium.com/briebug-blog/building-schematics-with-a-sandbox-a02b9e746851)来测试一下。它看起来会像这样。

```
<head>
  <link
    rel="canonical"
    href="https://kevinschuchard.com/blog/2018-11-20-schematic-sandbox/"
    data-rh="true"
  />
</head> 
```

Enter fullscreen mode Exit fullscreen mode

当 Medium 的导入工具可以解析您的内容时，您就万事俱备了。不幸的是，我发现导入工具时好时坏。如果 Medium 不能解析你网站的内容，没有其他方法可以在 Medium 上手动添加一个规范的链接。

### 开发到规范链接

使用 dev.to，你可以为一篇文章手动设置一个你喜欢的规范链接 URL。谢谢德夫托！谢谢德夫托！如果您正在自己的 dev.to 文章中寻找规范链接，请在私有选项卡中寻找。当在编辑模式下认证时，它不会被设置。事实上，这是我在我的[网站](https://kevinschuchard.com/blog/2019-08-13-blog-seo/)上的原创文章的交叉帖子。

## 总结起来

我认为托管你的内容有很多好处，你没有理由不尝试接触不同平台上的受众。如果你能利用规范链接，他们将允许你拥有自己的内容，并获得搜索引擎排名奖励。如果你做不到这一点，我建议你在其他网站上少发布一些内容，并提供一个指向原文的链接。这样，你的原创文章将有望被视为比较小的重复帖子有更多可索引内容的权威。

## 链接

*   [介质上的重复内容](https://help.medium.com/hc/en-us/articles/217991468-SEO-and-duplicate-content)
*   [重复内容-谷歌搜索控制台](https://support.google.com/webmasters/answer/66359)