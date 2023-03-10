# Sass 和媒体查询

> 原文：<https://dev.to/paul_duvall/sass-and-media-queries-hb2>

确保网站和应用程序具有响应性，以便它们在所有设备上都是可用的和视觉上有吸引力的，这是现代 web 开发的基石之一。用 CSS 编写媒体查询是确保我们的产品响应的最常用的技术，将媒体查询与 Sass 结合使用使它们更容易使用。

在本文中，我将简要介绍如何使用媒体查询来确保站点和应用程序的响应性，以及如何将它们与 Sass 预处理语言结合使用。

这些想法在实践中的一个例子可以在[这里](https://codepen.io/duvallpj/pen/xoqLBr)找到。

### 媒体查询

可以在 CSS 中应用媒体查询，以便基于视口的宽度为选择器的属性赋予不同的值。

```
h1 {
  font-size: 26px;
}

@media only screen and (max-width: 900px) {
  h1 {
    font-size: 24px;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子包含一个 CSS 规则，它为`<h1>`选择器设置了一个 26px 的值。这决定了字体的大小，直到屏幕宽度低于 900px 的断点，此时媒体查询开始并将字体大小调整为 24px。

倾向于为每个断点设置一个媒体查询，并且该查询包含所有需要调整的 CSS 规则。

这一切都很好，以这种方式建立的媒体查询对我很有帮助。然而，我的媒体查询通常位于离它们所适应的 CSS 规则相当远的地方。假设上面的 h1 规则位于 CSS 文档的顶部附近，比如在第 15 行附近。如果文档有几百行长，而媒体查询位于底部，可能在第 250 行左右，那么在这两者之间快速导航会很棘手。

我经常发现自己上下滚动，查看媒体查询中的规则，试图在文档中找到原始代码。

### Sass 和媒体查询

使用 Sass，可以将媒体查询直接嵌套在 CSS 规则中:

```
h1 {
  font-size: 26px;

  @media (max-width: 900px) {
    font-size: 24px;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这消除了两次命名 CSS 规则的需要，使代码更加简洁。它还确保了与这个特定规则相关的所有代码都位于同一个位置。

不再上下滚动，寻找两个(或三个或四个——如果多个媒体查询影响 h1 规则)相关的代码块。

### 梁子

以这种方式编写媒体查询提出了一个新问题——不是在一个地方定义一个媒体查询，而是可能有与 CSS 文档中的规则一样多的较小的媒体查询。

如果将来您需要将断点值从 900px 调整到 950px，这将是一个问题。这将需要浏览整个代码库，搜寻几十个媒体查询。

### 米辛来救援了！

幸运的是，mixins 可以解决这个问题，它允许我们在一个位置定义媒体查询，然后在代码库中任何需要的地方应用它。

```
 @mixin respond-medium {
    @media (max-width: 900px) { @content }
  } 
```

Enter fullscreen mode Exit fullscreen mode

内容指令–`@content`–允许我们将代码块传递到 mixin 中。一旦 mixin 设置好了，我们就可以应用它了:

```
h1 {
  font-size: 26px;

  @include respond-medium {
    font-size: 24px;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 多个媒体查询

我们可以更进一步，建立一个处理多个媒体查询的 mixin，允许我们轻松地设置不同的断点。

```
 @mixin respond($breakpoint) {
    @if($breakpoint == medium)  {
      @media (max-width: 900px) { @content }
    }
    @if($breakpoint == small)  {
      @media (max-width: 600px) { @content }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

这里我们建立了一个名为`$breakpoint`的变量。根据为此变量传递的内容(medium 或 small)，设置正确的媒体查询。

这样，我们就可以轻松地应用多个媒体查询:

```
h1 {
  font-size: 26px;
  @include respond(medium) {
     font-size: 24px;
   }
  @include respond(small) {
     font-size: 20px;
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

此示例将确保 h1 字体大小(通常为 26px)在宽度小于 900px 的视窗中为 24px，在宽度小于 600px 的视窗中为 20px。

### 总结

看一下[这个例子](https://codepen.io/duvallpj/pen/xoqLBr)Sass 中的媒体查询是在一个真实的例子中实现的。由于 Sass 能够在 CSS 规则中嵌套项目，媒体查询可以与 CSS 规则更紧密地联系起来，从而产生更加用户友好和易于理解的代码库。这一点，加上 mixins 的使用(使用`@content`和`@if`指令),已经完全改变了我将来使用媒体查询的方式。