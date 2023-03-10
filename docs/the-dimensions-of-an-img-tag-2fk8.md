# img 标签的尺寸

> 原文：<https://dev.to/lyubomir/the-dimensions-of-an-img-tag-2fk8>

前几天，我们在团队中玩一个简单的 bug。错误在于为什么交叉点观察器加载了页面上几乎所有不在视窗中的图像？

很简单， **src** 属性为空:

```
 <img src="" data-src="" class="lazy-load" alt="Some title" /> 
```

这使得页面上的所有图像以定义的行高大小一个接一个地呈现。因此，当触发交叉点观察点时，几乎所有图像最初都在视口中。

好了，我们得到了这个问题，所以让我们为 img 标签定义一些维度:

```
 <img src="" data-src="https://URL_TO_IMAGE" class="lazy-load" height="200" width="200" alt="Some title" /> 
```

啊，图像仍然只有行高的大小，而且几乎都是可见的...

然后在添加一些 CSS 后，尝试设置尺寸“最小高度”等。我们无法设置正确的尺寸。

突然有人想起来了，如果在 **src** 属性中没有图像的 URL，那么**宽度和高度**将不会被使用，标签也不会是那个大小。

简单的解决方案是:

```
 <img src="https://URL_TO_1x1_PIXEL_IMAGE" data-src="https://URL_TO_REAL_IMAGE" class="lazy-load" height="200" width="200" alt="Some title" /> 
```

是的，如果您在 src 中设置 1x1 图像，将应用高度和宽度，交叉点观察点将正常工作。

大多数关于图像延迟加载的文章都没有提到你需要填充 src 属性，这对于 UI 和延迟加载来说更好。