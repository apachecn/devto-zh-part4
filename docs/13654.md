# 此元素可见吗？

> 原文：<https://dev.to/alohci/is-this-element-visible-2lpi>

这个例子再简单不过了。问题是，包含文本“Hello World”的 div 元素是可见的还是隐藏的？

[https://codepen.io/alohci/embed/rEgaYa?height=600&default-tab=result&embed-version=2](https://codepen.io/alohci/embed/rEgaYa?height=600&default-tab=result&embed-version=2)

可见吧？或许吧。jQuery 不敢苟同！

[https://codepen.io/alohci/embed/OeYPVz?height=600&default-tab=result&embed-version=2](https://codepen.io/alohci/embed/OeYPVz?height=600&default-tab=result&embed-version=2)

问题在于 jQuery 对可见性检查的定义和实现。[定义](https://api.jquery.com/visible-selector/)是

> 如果元素占用了文档中的空间，则认为它们是可见的。可见元素的宽度或高度大于零。

实现是

`!! (el.offsetWidth || el.offsetHeight || el.getClientRects().length)`

有问题的 div 的 CSS 属性 display 值为“contents”，这意味着它没有创建自己的布局框。它的子元素的布局框直接连接到它的父布局框。CSSOM 规范规定 offsetWidth、offsetHeight 和 getClientRects()。对于这样的元素，长度必须全部返回零

> [对于 offsetWidth 和 offsetHeight](https://drafts.csswg.org/cssom-view/#dom-htmlelement-offsetwidth) 如果元素没有任何关联的 CSS 布局框，则返回零并终止该算法。
> 
> [for getClientRects()](https://drafts.csswg.org/cssom-view/#dom-element-getclientrects) 如果调用它的元素没有关联的布局框，则返回一个空的 DOMRectList 对象，并停止该算法。

有鉴于此，我们应该做些什么呢？直观的答案显然与技术细节不符。从开发人员的角度来看，我不清楚一个没有布局框的元素是否真的应该被认为是可见的，仅仅因为它的内容是可见的。然而，从用户的角度来看，显然应该这样做。除了“可见”和“隐藏”，我们还需要第三种状态吗？