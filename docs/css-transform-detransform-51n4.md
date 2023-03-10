# CSS 变换/反变换

> 原文：<https://dev.to/kaos/css-transform-detransform-51n4>

当您只希望对父元素进行变换时，您会希望对子元素应用负变换。
这对`skew`和`rotate`特别有用。

```
.container {
    transform: skewX(15deg)
}
.container > * {
    transform: skewX(-15deg);
} 
```

```
<div class="container">
    <div class="content">
        This content is not transformed
    </div>
</div> 
```

现在用 CSS 变量。这使用`calc()`来取反该值。

```
:root {
    --value: 15deg;
}
.container {
    transform: skewX(var(--value))
}
.container > * {
    transform: skewX(calc(-1 * var(--value)));
} 
```

添加媒体查询来处理具有不同值的不同屏幕尺寸(旋转元素可以根据其宽度使其变得很大)。

```
:root {
    --value: 15deg;
}

@media screen and (min-width: 1440px) {
    :root {
        --value: 5deg;
    }
}

.container {
    transform: skewX(var(--value))
}
.container > * {
    transform: skewX(calc(-1 * var(--value)));
} 
```

[https://codepen.io/kaos1910/embed/dybJjdG?height=600&default-tab=result&embed-version=2](https://codepen.io/kaos1910/embed/dybJjdG?height=600&default-tab=result&embed-version=2)

另一个例子使用 SkewY 对全宽容器
[https://codepen.io/kaos1910/embed/OJLzobw?height=600&default-tab=result&embed-version=2](https://codepen.io/kaos1910/embed/OJLzobw?height=600&default-tab=result&embed-version=2)