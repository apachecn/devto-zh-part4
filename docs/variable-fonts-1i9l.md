# 可变字体

> 原文：<https://dev.to/andi1984/variable-fonts-1i9l>

今天我想谈谈网络上的可变字体。但是那些是什么？

> 可变字体是 OpenType 字体规范的一种发展，它可以将多种不同的字体合并到一个文件中，而不是为每种宽度、粗细或样式创建一个单独的字体文件。它们允许您通过 CSS 和一个@font-face 引用访问给定字体文件中包含的所有变体。
> - <cite>[MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Fonts/Variable_Fonts_Guide)</cite>

## 变字体前的现状

让我们首先回顾一下自定制网络字体开始以来，我们是如何将*变体*引入网络字体的:

[https://www.youtube.com/embed/B42rUMdcB7c](https://www.youtube.com/embed/B42rUMdcB7c)

好吧，是的。因此，对于不同的变体，我们必须加载许多不同的字体文件，每个文件对应一种特定的字体类型，带来不同的字体变体。

那么可变字体如何解决通过网络请求一堆文件的问题呢？

## 技术背景

### 设计坐标轴

可变字体定义了*设计轴*，因此字体的变化尺寸就像它的*重量*。

*在*之前，这是通过*为每个变体的不同特征重新绘制*每个单独的字形来完成的。

*现在*——可变字体——*每个字形定义一个增量*，在这个增量内，每个字形的控制点可以变化。这个增量中的任何变化都可以被*内插*，而不是保存大量的字形变化，通过字体文件保存和发送的是这个增量。

有了它，我们完全有能力在整个 delta 范围内创建我们自己的、定制的变体，而不是仅仅使用以前由不同字体文件给出的固定变体。

关于更详细的介绍，我非常推荐你[W3C 的技术总监 Chris Lilley](https://svgees.us/index.html) ，特别是他在[的演讲“2018 年的网络字体:一切都会改变”](https://youtu.be/vNMJtxL5OgE)。

[https://www.youtube.com/embed/vNMJtxL5OgE](https://www.youtube.com/embed/vNMJtxL5OgE) (从 6:50 开始)

在 [Axis Praxis](https://www.axis-praxis.org) 你可以自己尝试改变字体变化。我可以推荐给你们所有不熟悉可变字体的人，让他们用侧边的范围滑块来看看字体和网站是如何变化的。

### 通过字体-变化-设置控制设计轴

所描述的设计轴由被描述为键/值对列表的*字体变化设置*控制。

它由四个字母轴名称及其各自的值组成，例如

```
html {
    font-family: "etc-grandstander", sans-serif;
    font-variation-settings: "wght" 600, "slnt" 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 浏览器支持

尽管可变字体是一个相当新的事物，但是根据 caniuse.com 的数据，全球 80%的用户支持浏览器。尽管 Internet Explorer 不支持可变字体！

如果你对在你的页面上使用可变字体感兴趣，我可以推荐你一篇来自 Clearleft 的好文章[，它也指出了后备解决方案。](https://medium.com/clear-left-thinking/how-to-use-variable-fonts-in-the-real-world-e6d73065a604)

## 野外可变字体！

早在 12 月份，我从伟大的 [ETC](https://etc.supply/) 团队那里买了 [ETC 看台](https://v-fonts.com/fonts/etc-grandstander)，他们也在出售其他一些不错的可变字体。

你可以在 v-fonts.com 找到可用可变字体的列表。

在那边的 [CSS 窍门](https://css-tricks.com/weird-things-variable-fonts-can-do/)你可以读到所有你可以用可变字体做的*怪异的事情*。关于这个话题，他们有越来越多的[指南。看一看！](https://css-tricks.com/guides/opentype-variable-fonts/)

### 开发者工具

我可以强烈推荐[火狐开发者工具](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Edit_fonts)中的字体检查。

他们提供了包括设计轴在内的完整信息列表，并带有一个范围滑块[，您可以在页面](https://twitter.com/andi1984/status/1078381928192061441)上动态修改。

### 看一看🎉

正如你可能已经看到的，我实际上在我的博客上使用我买的字体作为我文章的标题。

[用提到的 Firefox Devtools 看看吧！](https://twitter.com/andi1984/status/1078381928192061441)🤣
液体错误:内部

干杯，

安迪