# 模糊 DOM:用 CSS 和 HTML 隐藏事物的一种方式

> 原文：<https://dev.to/shwilliam/obscuring-the-dom-4jb8>

作为一名 web 开发人员，我想不出有哪个项目不需要一些逻辑来处理隐藏或显示 DOM 元素。无论是作为一个手风琴的一部分，一些标签，一个简单的 toast 通知或一个模态，切换 DOM 元素进出视图是我发现自己一直在实现的事情。

虽然看起来最容易或者最熟悉的方法可能很诱人，但是考虑这些决定影响网站用户的各种方式是很重要的。

本文将简要描述我遇到的一些最常见的方法，并解释使用它们的后果。

通常，我们可以在 3 个范围内隐藏 DOM 元素:

1.  为所有人隐藏
2.  对屏幕用户隐藏
3.  对辅助技术(AT)用户隐藏

这些选项中哪一个最合适只能根据具体情况进行评估，它将决定最适合您的实施方案。通过使用不适当的技术，你可能会排除一个重要的用户群访问你的网站的一部分。

## 1。为所有人隐藏

### `display: none;`

到目前为止，为每个人隐藏元素的最常见方式是将其 CSS `display`属性设置为`none`。这将**在视觉上隐藏元素**，并使其被 ATs 忽略**。这种技术还会将目标元素从文档的正常流中移除，这意味着它不会占用网页上的任何空间。** 

```
.hidden {
  display: none;
} 
```

### `hidden`

同样的效果可以通过 HTML 属性`hidden`的应用来实现，我也喜欢显式地对其进行样式化以支持遗留浏览器。

```
[hidden] {
  display: none;
} 
```

这两种方法都很棒，它们易于阅读，简短，并且在不同的浏览器中表现正常；但是，如果您希望在视图内外切换时转换一个元素的样式，那么`display: none;`将不会**起作用。避免这种限制的常见方法是显式地将元素的`max-width`或`max-height`与`overflow: hidden;`一起设置为`0`。通过提供一个没有空间可占据的元素，它的内容将完全折叠并被大多数屏幕阅读器忽略。为了确保 ATs 完全忽略这些内容，我喜欢将这种方法与`visibility: hidden;`结合起来。** 

```
.el {
  max-height: 999px;
  overflow: hidden;
  visibility: hidden;
  transition: 1s max-height ease;
}

.el--hidden {
  max-height: 0;
} 
```

### `visibility: hidden;`

如果你想隐藏一些东西，但最终还是要展示出来，你可能希望元素在可见时**尊重它所占据的空间**。这可以简单地通过定义 CSS 属性`visibility: hidden;`来实现。请注意，**在视觉上和对 ATs** 来说都隐藏了内容。

```
.hidden {
  visibility: hidden;
} 
```

## 2。对屏幕用户隐藏

为了**清理视觉 UI，但确保元素被 ATs** 拾取，最常见的技术是将元素移出屏幕，或者隐藏宽度或高度不为 0 的元素。有太多的 CSS 片段可以实现这一点，其中大多数都是下面这个例子的变体。

```
.sr-only {
  clip: rect(1px 1px 1px 1px); /* IE 6/7 */
  clip: rect(1px, 1px, 1px, 1px);
  height: 1px;
  width: 1px;
  overflow: hidden;
  position: absolute;
  white-space: nowrap;
} 
```

在目标元素是交互式的情况下，您很可能希望在聚焦时可见。这可以简单地通过将选择器修改为仅针对非活动或未聚焦的目标元素(例如`.sr-only:not(:focus):not(:active)`)来实现。

或者，您可以使用 HTML ARIA 属性，比如`aria-describedby`或`aria-labelledby`，强制屏幕阅读器宣布与隐藏元素相关的信息。

## 3。对辅助技术(AT)用户隐藏

### `aria-hidden`

在某些情况下，你会发现自己使用 HTML 元素仅仅是为了装饰的目的，例如图标字体。在这种情况下，你需要确保这些**不会被 ATs** 拾取。这可以通过简单地将元素的`aria-hidden`属性设置为`true`来实现。

## 总结

概括来说，我们已经复习了:

1.  为所有人隐藏
    *   `display: none`或 HTML `hidden`
    *   `visibility: hidden;`对于过渡元素
2.  对屏幕用户隐藏
    *   移出屏幕(并剪辑)
3.  对辅助技术用户隐藏
    *   `aria-hidden`

值得注意的是，由于我们处理的代码运行在许多不同的浏览器引擎上，在不同的环境中，基于一个活的规范，并且将被各种辅助技术解释，所以您自己测试这些方法是至关重要的。为了这个目的，我喜欢保留这个由 Terrill Thompson 创建的页面。它实现了这些技术(以及其他一些技术)，您可以使用这些技术轻松地进行试验。

我希望这篇文章能鼓励你在给一个元素加上`display: none;`样式之前三思，并且对设计你的站点的可访问性的影响更加谨慎。

如果你遇到任何问题或对这篇文章有疑问，请在这里随意提交。

## 进一步阅读:

[屏幕阅读器用户看不见的内容](https://webaim.org/techniques/css/invisiblecontent/)

[小心屏幕外的可访问文本](https://medium.com/@jessebeach/beware-smushed-off-screen-accessible-text-5952a4c2cbfe)

[包容性隐藏](https://www.scottohara.me/blog/2017/04/14/inclusively-hidden.html)

[现在你看到我了](https://alistapart.com/article/now-you-see-me/)