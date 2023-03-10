# 滚动就像你对浏览器视而不见一样！

> 原文：<https://dev.to/piyukore06/scroll-like-you-are-oblivious-of-the-browser-4j4n>

这是现代应用的时代。毫无疑问，在这些现代应用中，我们希望滚动条在所有浏览器和操作系统中的外观和行为都是一样的。滚动条可能是设计的一个重要部分。看看 [CSS Tricks](https://css-tricks.com/) 网站，他们将滚动条作为设计的一部分整合得多么漂亮。web 已经有 20 多年的历史了，但是仍然有滚动条，仍然很难设计。样式滚动条是 CSS 抱怨的典型代表，为什么它这么难？

[![](img/a2c0d7191b7f484bf5ffbd9d1fdb5808.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pnR3NgKu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/db3wejhun61zdcx3weaw.png)

在我们研究解决方案之前，首先让我们看看滚动条样式如何在不同的浏览器中实现。

### Safari、Opera 和 Chrome (Webkit、Blink 浏览器)

Webkit 浏览器支持滚动条的样式，你可以改变轨迹和缩略图的宽度颜色以及其他很多东西，例如:

```
::-webkit-scrollbar {
    width: 15px;
    height: 15px;
}

::-webkit-scrollbar-track-piece  {
    background-color: #C2D2E4;
}

::-webkit-scrollbar-thumb:vertical {
    height: 30px;
    background-color: #0A4C95;
} 
```

这里有一个详细的列表，列出了所有可以在[这里找到的`::-webkit-srollbar`前缀选项](https://webkit.org/blog/363/styling-scrollbars/)

> 注意:伪元素`::-webkit-scrollbar`是一个非标准的实现，它允许我们修改浏览器滚动条的外观

### 火狐(壁虎)

从 64 版本开始，Firefox 通过属性`scrollbar-color`和`scrollbar-width`支持[官方滚动条样式](https://drafts.csswg.org/css-scrollbars-1/)，例如:

```
 .scroller {
    width: 300px;
    height: 100px;
    overflow-y: scroll;
    scrollbar-color: rebeccapurple green;
    scrollbar-width: thin;
  } 
```

关于这方面的细节可以在这里找到

> 有一个警告(消失滚动条的奇怪例子):macOS 默认的自动隐藏半透明滚动条不能用这个规则着色(它们仍然根据背景选择自己的对比色)。只有永久显示的滚动条(“系统偏好设置”>“显示滚动条”>“总是”)是彩色的。

这可不太好。必须有一个解决方案。为什么这么难？？

### 自定义滚动条

解决方案之一可以是不使用本地浏览器滚动。我们可以创建自己的 div，定义一个基于 js 的滚动，这样我们可以完全控制滚动条的外观。但那很贵，保真度低，而且会让人感觉拖沓。

我寻找滚动条已经很长时间了，直到我发现了 Simplebar。

使用 Simplebar，我们可以利用一些非常规的 CSS 矩阵来构建一个自定义的滚动条，滚动时不需要任何 JavaScript，只需要一些设置代码。

根据 Simplebar 自述文件，SimpleBar 在很大程度上使用了浏览器的原生滚动功能，但是用自定义 CSS 样式的滚动条替换了传统的滚动条。该插件监听滚动事件，并相应地重画自定义滚动条。

这项技术的关键是隐藏原生浏览器滚动条。可滚动元素比其包含的元素稍宽/稍高，有效地隐藏了滚动条。它使用`MutationObserver`来监听浏览器事件。

## 开始实施吧

你可以选择任何一个`div`并用`new SimpleBar(document.getElementById('myId'))`初始化 simplebar。除了造型，还有各种各样的选择，如`autoHide`。

使用起来非常有趣。唯一需要记住的是，当你向一个 div 添加一个 Simplebar 时，确保这个 div 有一个`height`或`width`，这样 Simplebar 就可以决定添加哪个滚动条`horizontal`或`vertical`。

特别是在水平滚动条的情况下。让我们看一个例子。假设我们有一个容器。在里面，我们每个人都有 100 个 div。通常要让它水平滚动，我们需要给容器添加一些样式，比如`width: 1000px; overflow-x: scroll;`(当然还需要其他样式，比如`display: inline-block;`等，但这不是本文的重点)

但是如果我们有动态内容，问题就出现了。对于每一次渲染，我们需要改变 div 的宽度。

在这种情况下，Simplebar 非常好用。你所需要做的就是，在超过后，提到一个宽度，它应该滚动，假设`width: 100vw;`
它将使它滚动:)Simplebar 还提供了一个`recalculate()`函数，以防`MutationObserver`没有选择一个变化。

## 结束注

Simplebar 非常简单，它可以清楚地解决问题，而不会干扰浏览器的滚动行为。易于设置和定制。有适用于`React`和`Vue`的包装器。最终，我们有了一个跨浏览器、跨平台、轻量级和高性能的解决方案来解决现代应用程序中的滚动条问题。

照片由 Leo Rivas 在 Unsplash 上拍摄