# 使用 CSS 制作心跳动画

> 原文：<https://dev.to/paramharrison/heart-beat-animation-using-css-222>

在第一部分，我们学习了如何使用 CSS3 绘制心形

现在让我们看看，如何制作一个心跳动画。

这些是创建动画的步骤

*   为心跳创建关键帧动画
*   在不同的间隔使用变换属性缩放心脏到更大和更小的尺寸。
*   使用创建的关键帧在容器上创建无限动画。

#### 为制作心脏动画创建 CSS 关键帧

为了给出心脏跳动的效果，我们在不同的时间间隔测量心脏。这取决于我们是否能把握好间隔，以达到预期的效果。

> 请记住，在绘制心形时，我们将容器旋转了 45 度。我们需要在我们的关键帧变换中保持这一点，否则形状会在动画中倾斜

```
@keyframes animateHeart {
  // scale down and scale up faster in irregular intervals to get the throbbing effect
  0% {
    transform: rotate(45deg) scale(0.8);
  }
  5% {
    transform: rotate(45deg) scale(0.9);
  }
  10% {
    transform: rotate(45deg) scale(0.8);
  }
  15% {
    transform: rotate(45deg) scale(1);
  }
  50% {
    transform: rotate(45deg) scale(0.8);
  }
  100% {
    transform: rotate(45deg) scale(0.8);
  }
} 
```

#### 将关键帧添加到心脏容器中

这里动画属性接受三个值，

```
.heart {
  transform: rotate(45deg);
  // animation: <animation-name> <animation-duration> <animation-iteration-count>
  animation: animateHeart 1.2s infinite;
} 
```

1.  我们之前定义的关键帧`animateHeart`是`animation-name`
2.  `animation-duration`定义我们需要运行每个动画多长时间。如果我们定义 2s，那么每次运行 2s
3.  `animation-iteration-count`会跑那么多次。如果我们将其定义为`infinite`，那么它将在每隔 X 秒后无限运行动画，这是在`animation-duration`列中定义的

检查代码笔的完整代码以及心形。

[https://codepen.io/twhite96/embed/MNwXJr?height=600&default-tab=result&embed-version=2](https://codepen.io/twhite96/embed/MNwXJr?height=600&default-tab=result&embed-version=2)