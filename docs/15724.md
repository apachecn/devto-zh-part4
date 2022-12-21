# 深入研究@media 查询

> 原文：<https://dev.to/christopherkade/deep-dive-into-media-queries-36pe>

作为 web 开发人员，媒体查询是我们日常生活中的一个重要部分，然而，当谈到他们能做什么时，我们大多数人只是触及了表面。

对于那些不熟悉这个概念的人，让我简单解释一下。

## 什么是媒体查询？

举例来说，试着改变浏览器窗口的大小。当你移动它的时候，你会注意到元素的大小和位置经常随着屏幕的宽度和高度而移动。

这要感谢**媒体询问**他们的工作是根据设备的一般类型或某些参数(如大小)来修改你网站的布局。

媒体查询的一般语法如下:

```
@media screen and (min-width: 685px) {
  .title {
    font-size: 2rem;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `screen`是将应用样式的媒体类型。此媒体查询用于屏幕。
*   `and`是一个逻辑操作符，你可以把它们链接起来。例如，您可以对`min-width: 685px` **和** `orientation: landscape`进行媒体查询。
*   `min-width:`是一个媒体特性，在这个例子中，它告诉我们的浏览器只有在达到最小宽度 685 像素时才应用两个括号之间的样式。可以使用很多单位类型，比如`rem`。

好了，让我们通过 Codepen 上的具体例子来看看`@media`查询到底能做什么👇

## 媒体类型

我们大多数人都知道`screen`媒体类型，但是还有另外两种不太为人所知的类型。

媒体类型只是描述设备的类别。默认情况下，使用`all`类型，除非使用`not`或`only`逻辑运算符。

### `all`

意味着查询中的样式适用于所有设备。

[https://codepen.io/christopherkade/embed/XLzgXX?height=600&default-tab=result&embed-version=2](https://codepen.io/christopherkade/embed/XLzgXX?height=600&default-tab=result&embed-version=2)

### `print`

表示查询中的样式适用于以打印预览模式在屏幕上查看的分页材料和文档。

例如，如果我们有一个希望用户打印的“预订确认”页面，我们可以使用这个媒体查询隐藏底部的社交媒体链接。

[https://codepen.io/christopherkade/embed/XLzgEp?height=600&default-tab=result&embed-version=2](https://codepen.io/christopherkade/embed/XLzgEp?height=600&default-tab=result&embed-version=2)

右键单击并尝试打印这一页应该显示预览，没有底部文本。

### `screen`

我们通常去的地方。意味着这些样式是为屏幕设计的。

[https://codepen.io/christopherkade/embed/RzjZxb?height=600&default-tab=result&embed-version=2](https://codepen.io/christopherkade/embed/RzjZxb?height=600&default-tab=result&embed-version=2)

### `speech`

这种类型适用于屏幕阅读器。非常有限的 CSS 属性对屏幕阅读器行为有影响，但了解这种类型是值得的。例如，您可以隐藏屏幕阅读器不应该阅读的特定元素，或者大声读出代码标点符号。

[https://codepen.io/christopherkade/embed/ZdaJmo?height=600&default-tab=result&embed-version=2](https://codepen.io/christopherkade/embed/ZdaJmo?height=600&default-tab=result&embed-version=2)

## 必备媒体功能

我不会涵盖所有的媒体特性，因为其中一些很难理解，而且几乎没有什么用例。如果你对完整列表感兴趣，请点击查看[。](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries#Media_features)

### `width`和`height`

我们的面包和黄油，这些功能允许我们应用基于我们的视口的宽度和高度的样式。

比如 DEV 分别到达`950px`和`1120px`时隐藏`navigation`和`newest listings`侧视图(自己试试吧！).

[https://codepen.io/christopherkade/embed/ZdrgzY?height=600&default-tab=result&embed-version=2](https://codepen.io/christopherkade/embed/ZdrgzY?height=600&default-tab=result&embed-version=2)

### `orientation`

顾名思义，它允许我们根据设备的方向应用样式。不是`portrait`就是`landscape`。

[https://codepen.io/christopherkade/embed/ZdxgVO?height=600&default-tab=result&embed-version=2](https://codepen.io/christopherkade/embed/ZdxgVO?height=600&default-tab=result&embed-version=2)

### `display-mode`

基于应用程序的显示模式应用样式。

可用模式有:`fullscreen`、`standalone`、`minimal-ui`和`browser`。[更多信息](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/display-mode#Syntax)。

```
@media all and (display-mode: fullscreen) {
  body {
    margin: 0;
    border: 5px solid black;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 复杂媒体查询

通过使用逻辑操作符，如`and`、`not`、`only`和`,`(逗号)，我们现在可以为特定用例构建复杂的媒体查询。

在本例中，我们希望视频帧在平板电脑上处于横向模式时占据屏幕的整个宽度和高度(以获得更好的观看体验):

[https://codepen.io/christopherkade/embed/wLjwMv?height=600&default-tab=result&embed-version=2](https://codepen.io/christopherkade/embed/wLjwMv?height=600&default-tab=result&embed-version=2)

此外，您可以使用逗号将任何给定的样式应用于多个查询，例如:

```
@media (min-height: 680px), screen and (orientation: portrait) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

对于媒体的询问，这就差不多结束了。当然，这里还有更多可以学习的特性[但是这篇文章涵盖的工具将帮助你实现最灵敏的布局。](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)

我希望你至少从这篇文章中学到了一些东西，如果你已经学会了，请随时在 Twitter 上关注我🤗