# 轮廓按钮或幽灵按钮:悬停时的图标

> 原文：<https://dev.to/takaneichinose/outline-button-or-ghost-button-icon-on-hover-1jbl>

# 描述

轮廓按钮(或幽灵按钮):当鼠标悬停时，隐藏在左侧的图标会显示出来。

这些按钮有小号和大号。

图标字体我用了牛逼字体。我想可能会用到其他一些。

样式很容易定制，而且几乎所有可定制的样式都存储在一个变量中。

# 如何使用

```
<!--
Available classes:
.default (Dark grey button)
.primary (Blue button)
.success (Green button)
.warning (Yellow button)
.danger  Red button)
-->
<button class="btn-outline success">
    <i class="fas fa-check-circle"></i><!-- All of the icons from FontAwesome may be used -->
    <span>success</span>
</button> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

[https://codepen.io/takaneichinose/embed/VJazRv?height=600&default-tab=result&embed-version=2](https://codepen.io/takaneichinose/embed/VJazRv?height=600&default-tab=result&embed-version=2)