# 用 CSS 滤镜创建一个简单的黑暗模式

> 原文：<https://dev.to/boyum/create-a-simple-dark-mode-with-css-filters-4ji4>

所以有个东西你可能没听说过，叫“黑暗模式”。是的，你说得对，他们无处不在。让我来教你如何用更多黑暗中的光明来美化网络！🌓

**警告**:本教程使用了 [CSS 过滤器](https://developer.mozilla.org/en-US/docs/Web/CSS/filter)的力量，因此并不是每个浏览器都支持。截至 2019 年 7 月 10 日，[全球 94.36%的用户支持它](https://caniuse.com/#search=css%20filter)。

## 滤镜是如何工作的？

CSS `filter`属性可以让我们添加一些效果，比如单色、模糊、饱和以及其他一些功能。这些函数中的每一个都有参数，正因为如此，可能性实际上是无限的。[CSS 过滤器 MDN 页面](https://developer.mozilla.org/en-US/docs/Web/CSS/filter)有几个例子，并对它们进行了很好的描述。

```
.invert-me {
  filter: invert(100%);
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个过滤函数是✨ `invert()` ✨函数。它需要一个百分比(或 0 到 1 之间的十进制数)。MDN 告诉我们:

*【滤镜功能】反转输入图像中的样本。amount 的值定义了转换的比例。100%的值是完全反转的。值为 0%时，输入保持不变。介于 0%和 100%之间的值是效果的线性乘数。插值的空隙值为 0。*

这听起来非常适合创建一个简单的黑暗模式！毕竟黑反了就是白(反之亦然)，对吧？当然可以！让我们试着将整个页面翻转过来，看看是什么样子:

[https://codepen.io/sindre/embed/QXzPdj?height=600&default-tab=result&embed-version=2](https://codepen.io/sindre/embed/QXzPdj?height=600&default-tab=result&embed-version=2)

看起来已经差不多了！除了倒影，这一页看起来还不错。我们怎样才能阻止图像反转？我们也许可以通过将图像移出容器来变一些魔术，然后用绝对定位来正确定位它们，但是这对于 ux、dx 和 a11y 以及可能更多的缩写来说听起来很糟糕，所以我们需要找到另一种方法。我们也可以只反转页面的一部分，比如说每一个`<p>`、`<hx>`、`<strong>`、`<ul>`等等，但是当试图反转背景时，这可能会产生新的问题，因为元素之间可能有空白。

## 解

但是等等！白色的反转是黑色的，黑色的反转是白色的...这是否意味着任何颜色颠倒偶数次都是同样的颜色？当然是这样了！当翻转包装器时，让我们将所有图像翻转回它们的原始形式！

[https://codepen.io/sindre/embed/WqJxGZ?height=600&default-tab=result&embed-version=2](https://codepen.io/sindre/embed/WqJxGZ?height=600&default-tab=result&embed-version=2)

这似乎很有效！现在每个图像都将被反转回来。我们还可以使用类`no-dark-mode`来反转其他元素，比如视频、带有 CSS 背景图像的元素或者页面中已经由浅入深的部分。