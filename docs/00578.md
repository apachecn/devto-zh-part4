# CSS 中的尺寸单位:REM 和 EM

> 原文：<https://dev.to/cogoo/sizing-units-in-css-rem-and-em-4ea2>

CSS 有不同的单位来表示长度。许多单位都带有长度属性，如:`font-size`、`padding`、`width`等。有两种类型的长度单位:绝对和相对。

本文主要讨论两个相关的单元:REM 和 EM。我们讨论浏览器如何将这些转换成绝对单位。

## REM 到 PX

当转换为像素时，浏览器使用根元素的(`html`)字体大小作为乘数。

```
html {
  font-size: 16px;
}

h1 {
  font-size: 1rem; 
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`h1`的`font-size`将是`16px`。

浏览器运行的计算:

```
# root font size * property rem unit = px value
            16px * 1rem              = 16px 
```

Enter fullscreen mode Exit fullscreen mode

## EM 到 PX

当转换为像素时，浏览器使用被样式化的元素的`font-size`作为乘数。它将冒泡，直到获得第一个明确的字体大小定义。

```
html {
  font-size: 16px;
}

div {
  font-size: 22px;
}

div h1 {
  font-size: 2em
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`h1`的`font-size`将是`44px`。

浏览器运行的计算:

```
# element font size * property em unit = px value
               22px * 2em              = 44px 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，元素字体大小是从父元素`div`继承的

重要的是要记住，如果没有明确设置`font-size`，那么`font-size`继承就会生效。对于`rem`，如果根元素上没有显式设置`font-size`，它将从浏览器设置中继承值。