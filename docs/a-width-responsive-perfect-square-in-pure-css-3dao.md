# 纯 CSS 中的宽度响应完美正方形

> 原文：<https://dev.to/tchaflich/a-width-responsive-perfect-square-in-pure-css-3dao>

丹尼尔·麦卡洛通过 Unsplash 拍摄的封面照片。

[https://codepen.io/tchaflich/embed/LKyxqY?height=600&default-tab=result&embed-version=2](https://codepen.io/tchaflich/embed/LKyxqY?height=600&default-tab=result&embed-version=2)

## 兴趣点

### 可调整大小的块级 div

[`resize`](https://developer.mozilla.org/en-US/docs/Web/CSS/resize) 属性——我并不知道您实际上可以控制这个过去的文本区域，但是在寻找一种不使用 JavaScript 就能制作一个用户可调整大小的 div 的方法时偶然发现了它。

### 正方形是如何制成的

```
#square {
  width: 100%;
  padding-bottom: 100%;
  position: relative;
  background-color: #2980b9;
} 
```

Enter fullscreen mode Exit fullscreen mode

分解如下:

*   `width: 100%`确保它被定义为和它的外部可调整大小的容器具有相同的宽度。
*   基于 CSS 规范中的一个技巧。当按百分比指定边距或填充时，它总是基于宽度的[，即使它是-top 或-bottom 属性。这样我们得到了一个等于宽度的属性。](https://drafts.csswg.org/css-box-3/#propdef-margin)
*   这样我就可以在里面放东西而不会破坏填充物；任何额外的内容都会将内容添加到框中，这将使它成为一个矩形
*   有没有这样你就能看到\_(ツ)_/在哪里

### 圆是怎么做出来的

一旦你有了任何一个 T2 正方形，画一个圆就很容易了。你真正需要的唯一技巧是`border-radius: 50%`，但是让我们再多分解一下响应圈。

```
#circle {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
  border-width: 4px;
  border-color: #27ae60;
  border-style: solid;
  border-radius: 50%;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `position: absolute`确保该圆保持在`position: relative`正方形内，同时不向其添加任何流入内容。
*   `top: 0; bottom: 0; width: 100%; height: 100%;`将圆约束到响应正方形的精确尺寸。此时，我们拥有的是一个位置:绝对容器，它是我们正方形的内部副本。
*   `border-*`属性用于构建圆的轮廓。我们有一个 4px 的绿色实心边框，它的半径是正方形边的一半。

### 快速几何重述

下面的例子说明了为什么`50%`是从正方形变成圆形的神奇数字:

[![A very pixelated drawing showing a circle inside a square with some radials. It's very geometric.](img/3dffbf476254561105e696402de93d0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j_4EbVF7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tpgyjp4bqhxo5uea8o50.png)

圆的半径等于正方形边长的一半。