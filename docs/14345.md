# 在 CSS 中水平和垂直居中

> 原文：<https://dev.to/skhmt/horizontally-and-vertically-centering-in-css-2248>

如果你和我一样，在你职业生涯的某个阶段，HTML 元素的垂直和水平居中是你生存的祸根。

幸运的是，这个解决方案适用于所有的浏览器，甚至是 IE 11 :

```
display: flex;
justify-content: center;
align-items: center; 
```

Enter fullscreen mode Exit fullscreen mode

`justify-content`默认为水平对齐。移除或设置`justify-content: flex-start;`将子元素放在**左边的**。设置`justify-content: flex-end;`将子元素放置在**右侧**。

`align-items`默认为垂直对齐。移除或设置`align-items: flex-start;`将子元素放置在**顶部**。设置`align-items: flex-end;`将子元素放置在**底部**。

这里有一个[的例子](https://jsfiddle.net/zt8sb6qm/1/)。

大概可以不用`padding`、`absolute`左右上下定位、`float`、`line-height`、`transform`等任何招数了:)