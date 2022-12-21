# 使用 animate.css 的引导转盘

> 原文：<https://dev.to/disjfa/bootstrap-carousel-using-animate-css-36a4>

前提是我们知道如何[自举](https://getbootstrap.com)，我们知道如何 [Animate.css](https://daneden.github.io/animate.css/) 。现在在 bootstrap 中有一个类似旋转木马的东西。我们想让动画看起来更花哨！让我们试试。

### 这是怎么回事？

首先，我们需要弄清楚如何为[转盘](https://getbootstrap.com/docs/4.3/components/carousel/)使用引导类。当我们检查元素、测试和尝试时，我们看到主元素上有一个快速的`.carousel-item-left`和`.carousel-item-right`类。还有一个`.active`班。让我们困惑，我已经困惑了，所以我就解释一下。

`.active`项在转盘上移开或淡出，以便用于制作出类的动画。简单概述一下。

### 动画出类

```
.carousel-item-left.active {
  ...
}

.carousel-item-right.active {
  ...
} 
```

### 班里的动画

```
.carousel-item-left {
  ...
}

.carousel-item-right {
  ...
} 
```

### 简单，左右

所以现在我们知道如何检查和操作进出。接下来，我们需要设置一个主转盘，并删除 deafult 动画。让我们设置一个默认的传送带。为一个基本动画保留`.slide`类，并添加一个自定义的`.carousel-...`类，我们可以用它来挂接动作。

```
<div id="carousel-zoom" class="carousel slide carousel-animation bg-dark" data-ride="carousel">
  ...
</div> 
```

### 重置一些 css

```
.carousel-animation .carousel-item {
    transform: none;
}

.carousel-animation .carousel-item-left.active,
.carousel-animation .carousel-item-right.active {
    transform: translate(0, 0);
} 
```

这将重置所有的动画。恢复滑动。所以现在我们可以开始了。

我们可以保持简单，使用一些来自 [Animate.css](https://daneden.github.io/animate.css/) 的动画。如果你愿意，你可以创造任何你喜欢的东西。现在我们使用 bounceIn 和 bounceOut 动画。让我们把它们放在一起。

```
.carousel-animation .carousel-item {
    transform: none;
}

.carousel-animation .carousel-item-left {
    animation-name: bounceInRight;
    animation-duration: .65s;
}

.carousel-animation .carousel-item-left.active {
    transform: translate(0, 0);
    animation-name: bounceOutLeft;
}

.carousel-animation .carousel-item-right {
    animation-name: bounceInLeft;
    animation-duration: .65s;
}

.carousel-animation .carousel-item-right.active {
    transform: translate(0, 0);
    animation-name: bounceOutRight;
} 
```

### 就是这样！

酷，现在我们完成了。我们从右边进入，也就是从左边进入，从左边退出。反之，如果我们点击右边。

您可以使用 Animate.css 提供的所有选项。此外，如果您想查看工作示例，请查看此处的。

### 接下来呢？

下一个！尽情享受吧！做出令人敬畏的东西并变得令人敬畏。当初始动画完成后，你可以制作内部动画。在`.active`类被放入新的内部项目之后。查看[这个例子](https://disjfa.github.io/bootstrap-tricks/animate-carousel/)开始学习。

别忘了玩得开心。

[原文](https://disjfa.github.io/bootstrap/css/2019/08/20/bootstrap-carousel-using-animate.css.html)