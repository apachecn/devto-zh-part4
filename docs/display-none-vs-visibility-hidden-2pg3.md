# `显示:无'与`可见性:隐藏'

> 原文：<https://dev.to/stephencweiss/display-none-vs-visibility-hidden-2pg3>

在客户端隐藏元素时，有多种选择。最常见的两种是通过选择性切换`display`和`visibility`的 ofCSS 属性。

有什么区别？来自 LearnLayout.com<sup>1</sup>

> 这个[ `display` ]和`visibility`不一样。将`display`设置为`none`将会呈现页面，就好像元素不存在一样。`visibility: hidden;`将隐藏该元素，但该元素仍将占据它完全可见时的空间。

因此，如果元素被绝对定位，显示时消耗的“空间”不会影响任何东西。

然而，为了确保布局不会移动，使用`visibility`属性是有意义的。

`display` <sup>2</sup> 选项:

*   `block`
*   `inline`
*   `flex`
*   `none`
*   …以及更多。参见 MDN 的详尽列表:

`visibility` <sup>2</sup> 选项:

*   `visible`
*   `hidden`
*   `collapse`

## 资源

*   <sup>1</sup> [CSS -【显示】属性| LearnLayout](http://learnlayout.com/display.html)
*   <sup>2</sup>T2】显示| MDN
*   <sup>3</sup> [能见度| MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/visibility)