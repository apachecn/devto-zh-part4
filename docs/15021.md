# 使用 flexbox 垂直和水平居中文本

> 原文：<https://dev.to/eduardo__uribe/vertically-and-horizontally-center-text-with-flexbox-1fmd>

今天，您将学习如何轻松地将文本垂直和水平居中。

[![](img/7d1c09877e9fdc046f6fa8036d35feac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X5jDwydL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nsbng03fflb6l8si4v2s.jpg)

### 起始代码

超文本标记语言

[![](img/25a93d2ebfeba0c1888668b523aeed7e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1WfRXmm3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/152y0yukb1j9g5jbe2uy.jpg)

半铸钢ˌ钢性铸铁(Cast Semi-Steel)

[![](img/45497ad3dda16bd4032f0923adcc51c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZWKULlTa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/344j9zr87a88fb1lw22o.jpg)

### 工作原理

flex 容器中的文本通过自动包装在[匿名块容器](https://www.w3.org/TR/css-display-3/#css-anonymous)中而成为 flex 项目。

[![](img/2c49b17758e6b0bb54cc6aa6e0b0380e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qu3oFO1k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vu0o2cl42lmwcyfn7yt5.jpg)

这将文本转换为 flex 项，并允许它参与其容器的 flex 格式上下文。

现在，任何 flexbox 属性都可以应用于包含文本的匿名块。

在这种情况下，我们使用`justify-content: center;`水平居中，使用`align-items: center;`垂直居中

[![](img/8a8a9d77aa65bb5f917e82e8e1118dc6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rXHRjerU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fzmkuryltm3wp2ax9iwu.jpg)

这就是使用 flexbox 在元素内部垂直和水平居中文本的方式。

[喜欢收到关于新的 HTML/CSS 小技巧的通知在 twitter 上关注我@Eduardo__Uribe](https://twitter.com/Eduardo__Uribe)