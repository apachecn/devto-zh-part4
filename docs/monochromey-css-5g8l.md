# 单色 CSS

> 原文：<https://dev.to/atornblad/monochromey-css-5g8l>

假设你有一张照片，你想在网页的某个地方展示。照片色彩丰富，但你需要遵循严格的配色方案，可能是因为品牌准则，也可能只是因为你比其他人更喜欢某些颜色。

第一个诱惑可能是在你最喜欢的图像编辑软件*(我的是[Paint.NET](https://www.getpaint.net/)电脑版)*中打开照片，摆弄各种功能，直到照片看起来像你想要的样子。

然后，过一段时间，配色方案因为某种原因改变了。也许高层在品牌重塑研讨会后出现在会议室，或者也许你厌倦了紫红色，决定让 T2 的碧蓝成为你最喜欢的颜色。你发现你的网站有数百张照片需要重新编辑。为了避免这种情况，你应该提供原始照片，让 [CSS 滤镜](https://developer.mozilla.org/en-US/docs/Web/CSS/filter)为你完成这个任务。

如果你使用`grayscale`滤镜，你会得到一张全是灰色的照片。如果你想要一个完全去饱和的图像，这很好。但如果你瞄准的是某个色调的单色图像，`grayscale`就是死路一条。相反，首先应用`sepia`滤镜，将图像每个像素的色调设置为 60 左右。然后，您可以添加另一个滤镜来增加或减少色调。

```
img {
    filter: sepia() hue-rotate(123deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

我做了一支[笔来演示效果](https://codepen.io/atornblad/pen/yLBGwOj)。将色调选择器拖动到所需的颜色，并复制生成的 CSS。如果在图像上按住手指*(或鼠标键)*，滤镜效果取消，显示原始图像。图像本身是一个随机的[不清晰的](https://unsplash.com/)图像，以`nature, water`作为查询。

[https://codepen.io/atornblad/embed/yLBGwOj?height=600&default-tab=result&embed-version=2](https://codepen.io/atornblad/embed/yLBGwOj?height=600&default-tab=result&embed-version=2)

*封面照片由[大卫·T](https://unsplash.com/@david113)在[Unsplash](https://unsplash.com/photos/V1nvtZ3TQ_8)T5 拍摄*