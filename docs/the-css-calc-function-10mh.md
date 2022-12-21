# CSS calc()函数

> 原文：<https://dev.to/baransel/the-css-calc-function-10mh>

`calc()`函数允许您对值执行基本的数学运算，当您需要从百分比中增加或减去长度值时，它特别有用。

它是这样工作的:

```
div {
    max-width: calc(80% - 100px)
} 
```

Enter fullscreen mode Exit fullscreen mode

它返回一个长度值，因此可以在任何需要像素值的地方使用。

你可以表演

*   使用`+`的加法
*   使用`-`进行减法运算
*   乘法使用`*`
*   除法使用`/`

> 一个警告:对于加法和减法，运算符周围的空格是必需的，否则它不会按预期工作。

示例:

```
 css
div {
    max-width: calc(50% / 3)
}
div {
    max-width: calc(50% + 3px)
} 
```

Enter fullscreen mode Exit fullscreen mode