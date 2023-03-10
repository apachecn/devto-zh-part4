# 为什么“位置:粘性”不起作用

> 原文：<https://dev.to/andreieres/why-position-sticky-doesn-t-work-5eio>

## 粘粘的还没个门槛

粘性需要一个指定的阈值，至少有`top`、`right`、`bottom`或`left`中的一个。

[https://codepen.io/eres/embed/mNjLpz?height=600&default-tab=result&embed-version=2](https://codepen.io/eres/embed/mNjLpz?height=600&default-tab=result&embed-version=2)

## 粘性空间不够

父对象必须大于其粘性对象。通过 Devtools 检查尺寸。可能父节点没有其他子节点，并且已经安装了粘滞。也许橡皮筋是一个柔性盒，它会随着父母的身高而增长。

[https://codepen.io/eres/embed/rXrQQB?height=600&default-tab=result&embed-version=2](https://codepen.io/eres/embed/rXrQQB?height=600&default-tab=result&embed-version=2)

## 粘有`overflow: hidden`之父，或祖父母之一

父节点上任何不可见的值`overflow`将粘性锚定到父节点的上下文。那么只有父母的滚动导致粘滞。窗口的滚动不会。

[https://codepen.io/eres/embed/VoBqwz?height=600&default-tab=result&embed-version=2](https://codepen.io/eres/embed/VoBqwz?height=600&default-tab=result&embed-version=2)