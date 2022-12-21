# #CodepenChallenge 以小见大:手绘视差徽章

> 原文：<https://dev.to/takaneichinose/codepenchallenge-think-small-hand-drawn-parallax-badge-1mbk>

图像的灵感来自于乡村联排别墅，是我画的(用 MS Paint，然后用 Powerpoint 把背景做成透明的)。它的视角会根据鼠标指针的位置而移动。

我用 Base64 来显示图像。

机芯由 VueJS 制造。

* * *

# 工作原理

大部分源代码都是不言自明的，所以我只放最重要的部分。

这些层由许多大小不同的图像组成。这对“视差”效应至关重要。

有了下面的源代码，我们就可以做视差移动了。

```
calc: function(el, w2, h2, x, y) {
  const w1 = el.w;
  const h1 = el.h;
  const w  = w1 - w2;
  const h  = h1 - h2;

  return {
    top:  (w * y * -1) + 'px',
    left: (w * x * -1) + 'px'
  };
}, 
```

Enter fullscreen mode Exit fullscreen mode

我只是取了图像大小之间的空间距离，然后根据鼠标的位置改变顶部和左侧的位置。

由于 Base64 源代码，CSS 源代码不是那么可读。但重要的是第 33 行。

* * *

我希望你喜欢它。感谢您的阅读。

* * *

# 演示

[https://codepen.io/takaneichinose/embed/jONGYMb?height=600&default-tab=result&embed-version=2](https://codepen.io/takaneichinose/embed/jONGYMb?height=600&default-tab=result&embed-version=2)