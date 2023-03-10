# current color(CSS 变量变酷之前的 CSS 变量)

> 原文：<https://dev.to/adrianbdesigns/css-currentcolor-a-css-variable-before-css-variables-were-cool-1k31>

CSS 变量正在成为一种标准，目前超过 90%的浏览器都支持它。在 CSS 变量之前，有一个和 CSS 变量有一些共同点的 CSS 值，叫做`currentColor`。

```
.message--danger {
    border: 5px solid currentColor;
    color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 关于当前颜色值

那么，`currentColor`是如何工作的呢？基本上，CSS 属性的值为`currentColor`的元素继承了`color`属性的值(属于同一个元素)。基本上，`color`的计算值将被用作`currentColor`的值。

该值可应用于以下 CSS 属性:边框、轮廓、框阴影和背景。

当边框、阴影、轮廓或背景共享相同的颜色值时，这很有用。在其他属性如背景、边框、轮廓等的情况下，这对于保持代码干燥是很好的。需要与文本颜色相同。

# 简单例子

[https://codepen.io/AdrianBece/embed/eqPLza?height=600&default-tab=result&embed-version=2](https://codepen.io/AdrianBece/embed/eqPLza?height=600&default-tab=result&embed-version=2)

在这个例子中，我们简单地改变了 BEM 修改器类的颜色，这改变了文本颜色，左边的背景颜色和边框颜色。

# 浏览器支持

超过 98%的全球使用的浏览器支持这个 CSS 值。更多信息请点击这里:[https://caniuse.com/#search=currentColor](https://caniuse.com/#search=currentColor)

感谢你花时间阅读这篇文章。如果你觉得这很有用，请给它一个❤️或🦄，分享评论。