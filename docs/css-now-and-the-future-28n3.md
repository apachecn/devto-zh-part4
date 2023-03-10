# CSS:现在和未来

> 原文：<https://dev.to/steffenpedersen/css-now-and-the-future-28n3>

我之前写过为什么这么多开发人员害怕 CSS。这通常是基于很少或没有知识或过时的知识。我们能用新知识帮助克服恐惧吗？让我们一探究竟！

# CSS 现在🎉

这将非常像天气预报。我们必须先看看现在的天气如何。从那里我们可以了解本周余下时间的天气情况。好吧，也许这个比喻有点过了。你明白了。

## 网络无处不在！

哦不，特定尺寸的桌面屏幕的日子已经一去不复返了。你们很多人都听说过媒体询问。如果你想攻击某个尺寸的屏幕，你可以这样做。但是你也可以像这样瞄准屏幕的方向`@media screen and (orientation: landscape)`。通过特定的组合，我们可以将 Apple Watch 作为目标！**简单！**因此，我们不应该真的担心，我们现在必须瞄准不同的设备。

## 复位

当你使用 CSS 时，你会很快发现浏览器的默认样式是不一样的。有不同的方法来处理这些问题。最流行的两个是 Reset CSS 和 normalize . CSS。Reset CSS 将清除 HTML 元素的所有内置样式。css 消除了 HTML 元素的浏览器不一致性。Normalize.css 将保留一些有用的默认值，而不是删除 CSS Reset 之类的所有内容。我几乎总是使用 Normalize.css。

## 预处理器

几年前，这是一个非常热门话题。CSS 预处理器允许你从一个独特的语法中生成 CSS，具有混合、函数、嵌套、继承等特性。这将(从主观上)使代码更容易阅读和维护。这是一个热门话题，而且火还在燃烧。我们已经习惯了它的存在。我们还在用 Sass，Less 和 PostCSS。我本人非常高兴与 SCSS 语法和 PostCSS 顶嘴。但是我看到同样的故事在 ECMAScript 和 jQuery 上重演——现在是 CSS 和预处理程序。CSS 现在支持称为 CSS 自定义属性的变量。我们将正确地看到 CSS 朝着预处理器的方向发展。

## 柔性盒和网格

约瑟(丹麦)，火越来越大，越来越旺！我们都听说过 CSS Flexbox 和 CSS Grid。它们都是用来布局的——但区别是什么呢？网格和 Flexbox 的主要区别在于网格是二维的，Flexbox 是一维的。使用 grid 可以处理列和行，使用 Flexbox 可以处理列或行。我的看法是，你应该使用 Grid 来布局你的页面，你应该使用 Flexbox 来处理你的组件的内容。不要忘记，现在使用 Flexbox 对事物进行定心是非常容易的。此示例水平和垂直居中。

```
.container {
  display: flex;
  justify-content: center;
  align-items: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 建筑

开发人员仍在努力解决的问题之一是他们代码库的文件结构。使用 Sass，你可以`@import`你的文件，并以这种方式创建一个文件系统。我们现在也有 ITCSS，它代表**I**v**T**ri angle**CSS**。这是一个帮助你组织项目 CSS 文件的架构。这个概念是，我们不应该在前两层输出任何 CSS，然后我们指定全局选择器，越来越具体。这样我们就不必用`!important`进行愚蠢的覆盖。有了这种结构，它还可以很好地理解级联和继承是如何工作的。

# CSS 未来🚀

我绝不是算命的。这只是我在信口开河。但是我们知道，网络正在向奇怪的设备传播。我们将不得不在未来妥善处理这一问题。我们正慢慢转向 CSS 而不是预处理器。未来会给我们带来什么？让我们一探究竟！

## CSS 属性

CSS 变得前所未有的强大。他们一直在添加新的属性和值。一个我等待更好支持的新值是`display: contents`。我喜欢这种价值观背后的理念。`display: contents`使元素的子元素看起来像是元素父元素的直接子元素。我们忽略了元素本身。这对列表元素特别有用。我也在等待类似`conic-gradient()`的属性和 5 级媒体查询。它有很多像`light-level`这样的新功能。

## 可变字体

我不是字体专家，但这听起来真的很酷。可变字体可以将不同的字体合并到一个文件中。这样你就不需要为每一种粗细和样式准备单独的字体文件了。我们可以用`font-variation-settings`定制字体，它允许像 wght(重量)、wdth(宽度)、ital(斜体)、slnt(倾斜)这样的值，你可以用不同的方式定制。你可以看看[这本指南](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Fonts/Variable_Fonts_Guide)。

```
.container {
  font-weight: 400;
}

.container {
  font-variation-settings: 'wght' 400;
}

.container {
  font-variation-settings: 'wght' var(--weight);
} 
```

Enter fullscreen mode Exit fullscreen mode

## CSS 胡迪尼

好吧，这真的很有趣。胡迪尼 CSS 基本上是一个新的浏览器 API 集合，它允许你获得更多的访问你的浏览器的 CSS 引擎。我们可以通过 JavaScript 访问这些 API。这个新概念是一个新的 W3C 任务组，其最终目标是让浏览器支持问题永远消失。胡迪尼 CSS 目前有 7 个 API，每个将处理一个特定的问题。我对此知之甚少，但我一定会尝试一下！

* * *

以上是我自己对 CSS 的一点想法。我不知道关于它的一切。但是我喜欢分发一些知识金块🐔

> 如果你没有做你喜欢的事情，你就是在浪费时间。
> 
> — Steffen Pedersen 🌚 (@mrsteffenp) [June 10, 2019](https://twitter.com/mrsteffenp/status/1138098572354883586?ref_src=twsrc%5Etfw)

感谢您的宝贵时间！

如果你喜欢这个，那么请❤️和 [*在 Twitter 上关注我*](https://twitter.com/mrsteffenp) 。