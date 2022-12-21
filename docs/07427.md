# 如何用 CSS 在你的网页内容中支持苹果的动态类型

> 原文：<https://dev.to/colingourlay/how-to-support-apple-s-dynamic-text-in-your-web-content-with-css-40c0>

*更新:2020 年 12 月*

现在苹果已经在大苏尔的 MacOS 上推出了这些功能，在应用下面的技巧之前，想一想动态字体是否是你想要在更大的显示器上使用的东西。一个`@media`查询可能有助于只限定较小的显示器，如果你想限制 iPhones 的把戏)

*原文...*

iOS 有一个很好的辅助功能，可以放大(或缩小)设备的文本，但我们通常只看到它用于原生应用程序内容和界面元素。

然而，实际上有一种方法可以将这些用户定义的字体大小应用到您的网页内容中！

首先，将您的`html`元素的`font`属性设置为苹果的系统字体:

```
html {
  font: -apple-system-body;
} 
```

Enter fullscreen mode Exit fullscreen mode

就其本身而言，这个 CSS 片段将支持动态文本大小调整，**但**它也强制苹果的系统字体系列，这可能不是你想要的(非苹果设备将最终使用默认的衬线字体，除非你在你的字体堆栈中指定后退)。

幸运的是，有一种方法可以只使用*的动态文本大小调整特性，而不用覆盖你想在所有设备上使用的任何字体系列。*

首先，我们将把前面的例子包装在 CSS [`@supports`](https://developer.mozilla.org/en-US/docs/Web/CSS/@supports) 条件块中，因此它只针对苹果设备:

```
@supports (font: -apple-system-body) {
  html {
    font: -apple-system-body;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

<small>*如果你以前没见过`@supports`，它是现代浏览器支持的一个特性，允许你编写条件 CSS，基于浏览器对特定声明的支持(这里是`font: -apple-system-body`)。*</small>

然后，您可以自由地在后代元素上指定自己的基于`font-family`和`rem`的相对`font-size`。

```
body {
  font-family: Lobsters;
  /* or Papyrus / "Comic Sans MS" / any other objectively great font */
}

p {
  font-size: 1.25rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！你现在应该能够为苹果设备编写更易访问的网站，而不会降低在其他平台上的外观。

注意:如果你已经在`html`元素的`px`中设置了一个自定义的`rem`大小，并且想要在非苹果设备上保持这个大小，那么`font`的定义必须是`!important` :

```
body {
  font-size: 20px;
}

@supports (font: -apple-system-body) {
  html {
    font: -apple-system-body !important;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode