# 🎩JavaScript 增强的 Scss mixins！🎩css 变量的智能可访问性

> 原文：<https://dev.to/adam_cyclones/javascript-enhanced-scss-mixins-series-intelligent-accessibility-with-css-variables-374c>

你是否曾经想选择一种背景颜色，而不必担心它是否符合 WCAG 2.0 AAA 标准，你想只是在 scss 中这样做，而不必担心它。

现在你可以了。

### 用法

1.  将 14 行 mixins 放到您的代码库中
2.  将 68 行 JavaScript 放到您的代码库中
3.  包括小颜色的 js 库
4.  执行以下操作:

```
.test {
    @include colorAuto();
    background-color: #83a787;
    color: #000; // optional
    font-size: 14px; // optional 
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/acronamy/embed/YmGoKv?height=600&default-tab=result&embed-version=2](https://codepen.io/acronamy/embed/YmGoKv?height=600&default-tab=result&embed-version=2)

### 工作原理

从概念上讲，它使用了与
相同的技术

[![adam_cyclones](img/31516d4e57cb048579f963723ed1de7b.png)](/adam_cyclones) [## 🎩JavaScript 增强的 SCSS 混合！🎩用 CSS 变量制作 CSS 作用域。

### 亚当·克罗克特 7 月 26 日 192 分钟阅读

#css #sass #javascript #codepen](/adam_cyclones/javascript-enhanced-scss-mixins-making-css-scopes-5fkm)

### 奖励提示。

*   chrome 颜色选择器内置了一个标准检查器！
*   这是世界上最好的可访问的颜色库[http://clrs.cc/a11y/](http://clrs.cc/a11y/)