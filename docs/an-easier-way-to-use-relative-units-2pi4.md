# 使用相对单位的简单方法

> 原文：<https://dev.to/mgrsskls/an-easier-way-to-use-relative-units-2pi4>

现在的浏览器在使用`cmd` - `+`和`cmd` - `-`的时候完美的放大缩小。正因为如此，开发人员有时会认为使用相对单位实际上是没有必要的。但由于这种缩放方式只影响当前网站，浏览器也有一个设置来改变浏览器的默认字体大小。不幸的是，当使用绝对单位时，这不起作用。这就是为什么使用相对单位*很重要。*

正如在我开发生涯的开始，使用`px`做任何事情和用像素度量得到 Photoshop 布局仍然是完全正常的，我在开发时仍然用像素思考。当然，有一些工具可以帮你做到这一点:例如，你可以在你的代码库中使用`px`，当使用后处理器或前处理器时，它会被转换成`rem`。

但是由于最好不要依赖工具，我更喜欢使用我一直最喜欢的 css 技巧之一:

```
html {
  font-size: 62.5%;
} 
```

Enter fullscreen mode Exit fullscreen mode

`62.5%`等于`10px`，因为浏览器的默认字体大小是`16px` ( `16 * 0.625 = 10`)。

现在你可以使用`rem`并且你的值与你的像素值相同，小数点分隔符只是向左移动了一位:

```
body {
  font-size: 1.6rem; /* 16px */
}

.wrapper {
  max-width: 72rem; /* 720px */
} 
```

Enter fullscreen mode Exit fullscreen mode

现在不要忘记在`body`上显式设置`font-size`，因为`10px`对于默认值来说太小了。

注意:我有时看到开发人员使用`html: { font-size: 10px; }`来达到同样的效果，但这并不能解决缩放问题。