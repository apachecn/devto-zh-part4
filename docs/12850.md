# 如何使用 CSS3 绘制心形

> 原文：<https://dev.to/paramharrison/how-to-draw-a-heart-using-css3-3g1n>

这是使用 CSS3 绘制和制作心形动画的第 1 部分。在这篇文章中，我们将学习如何使用纯 CSS 技巧绘制心形

像心形这样的形状可以使用纯 CSS 来实现，因为它在顶部包含弯曲的圆角，在底部包含斜线。

让我们来一步一步地了解如何创建它

*   创建一个简单的正方形容器(50px X 50px)
*   将容器旋转 45 度。这将使底部倾斜的线条
*   现在，为了制作心形中的两条上曲线，我们可以使用伪`before`和`after`元素。

让我们深入研究代码，看看它是如何一步一步进行的。

#### 创建心脏容器

一个 50 X 50 正方形，然后使用 CSS 变换属性将其旋转 45 度。这个倒置的正方形看起来会像一个菱形。

```
.heart {
  background-color: #fff;
  // Square with size 50px
  width: 50px;
  height: 50px;
  position: relative;
  // Rotate 45deg to make it looks like diamond
  transform: rotate(45deg);
} 
```

[https://codepen.io/Param-Harrison/embed/MNwXYE?height=600&default-tab=result&embed-version=2](https://codepen.io/Param-Harrison/embed/MNwXYE?height=600&default-tab=result&embed-version=2)

#### 创建伪元素

创建相同大小的伪元素，边界半径为 50%。现在这些元素将不可见，因为它隐藏在心脏容器下。

为了使其可见，请更改各个伪元素的顶部和左侧位置。

*   左边为`before`元素
*   `after`元素的顶部位置

我们需要移动一半大小的心形容器来得到心形。

```
.heart {
  &::before,
  &::after {
    position: absolute;
    content: '';
    // Pseudo elements of same size as container
    width: 50px;
    height: 50px;
    background-color: #fff;
    // It will make the pseudo element as circle
    border-radius: 50%;
  }

  &::before {
    // Only reveal half the circle to look like left side top of heart
    left: -25px;
  }

  &::after {
    // Only reveal half the circle to look like right side top of heart
    top: -25px;
  }
} 
```

[https://codepen.io/Param-Harrison/embed/BXaqXP?height=600&default-tab=result&embed-version=2](https://codepen.io/Param-Harrison/embed/BXaqXP?height=600&default-tab=result&embed-version=2)