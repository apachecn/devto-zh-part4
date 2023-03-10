# 使用 CSS 自定义属性和 Sass 构建 CSS 主题

> 原文：<https://dev.to/coryrylan/build-css-themes-with-css-custom-properties-and-sass-1dg7>

CSS 自定义属性(CSS 变量)是 CSS 中的一个新特性，它允许我们创建动态变量。类似于 Sass 变量，我们可以存储任何值，并在 CSS 代码库中重用该值。在这篇文章中，我们将展示一些迁移 Sass 主题以使用 CSS 自定义属性的简单技巧。

让我们从一个简单的带有几个 Sass 变量的按钮示例开始，我们希望将这些变量转换成 CSS 自定义属性。

```
<button class="btn">
  Hello
</button> 
```

Enter fullscreen mode Exit fullscreen mode

```
$color-neutral-100: #fff !default;
$color-neutral-500: #2d2d2d !default;
$button-padding: 12px 24px !default;

.btn {
  padding: $button-padding;
  background-color: $color-neutral-500;
  color: $color-neutral-100;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个底层 Sass 主题的消费者将导入 Sass 文件，更改变量，然后编译出一个新的 CSS 文件供他们的 web 页面使用。如果我们将我们的 Sass 主题转换成 CSS 自定义属性，那么我们的用户就可以改变我们 CSS 的主题，而不需要为 Sass 进行构建。用户可以动态改变 CSS 属性。

让我们举一个 CSS 自定义属性的简单例子。

```
/* :root is similar to html HTML selector but has a higher specificity */
:root {
  --primary-color: blue;
}

button {
  background-color: var(--primary-color);
} 
```

Enter fullscreen mode Exit fullscreen mode

CSS 自定义属性是通过在属性前加两个破折号`--`来定义的。一旦定义好，我们就可以在 CSS 的其他地方引用它。在这个代码片段中，我们的按钮背景色现在是蓝色，来自 referencing`--primary-color`属性。

如果变量没有值，CSS 自定义属性也可以有回退值。

```
button {
  background-color: var(--button-background-color, blue);
}

:root {
  /* if we uncomment this line the button will change from red to blue */
  /* --button-background-color: red; */
} 
```

Enter fullscreen mode Exit fullscreen mode

变量中的第二个参数是回退值。如果没有人用自定义值设置`--button-background-color`，那么按钮背景颜色将默认为蓝色。这种回退机制对主题化是有益的。

当从 Sass 变量迁移时，我们可以保留现有的变量，但使它们成为内部编译时变量。

```
// private variables only available at build time of your theme
$internal-color-neutral-100: #fff;
$internal-color-neutral-500: #2d2d2d;
$internal-button-padding: 12px 24px;

// public custom properties in theme available for consumer
.btn {
  padding: var(--button-padding, $internal-button-padding);
  background-color: var(--button-background-color, $internal-color-neutral-500);
  color: var(--button-color, $internal-color-neutral-100);
} 
```

Enter fullscreen mode Exit fullscreen mode

这个 Sass 将生成下面的 CSS:

```
.btn {
  padding: var(--button-padding, 12px 24px);
  background-color: var(--button-background-color, #2d2d2d);
  color: var(--button-color, #fff);
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的示例中，如果没有定义自定义主题值，我们将现有的 Sass 变量设为私有，作为默认值使用。现在我们仍然可以重用和共享我们的 Sass 变量，并且只在 Sass 中可用。我们只公开我们希望用户能够通过 CSS 自定义属性自定义的主题部分。

现在，用户可以扩展和自定义我们通过 CSS 自定义属性显式公开的主题。这对于通过 [Shadow DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM) 进行 CSS 封装的组件来说非常重要和有用。这允许您的组件只公开一个可以被主题化的子集 API。

```
:root {
  --button-background-color: blue; /* all buttons default to blue */
}

.btn.red {
  --button-background-color: red;
}

.btn.green {
  --button-background-color: green;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<button class="btn">
  Hello Custom Blue
</button>

<button class="btn red">
  Hello Custom Red
</button>

<button class="btn green">
  Hello Custom Green
</button> 
```

Enter fullscreen mode Exit fullscreen mode

使用带有 Sass 的 CSS 自定义属性，我们可以创建灵活且可维护的自定义 CSS 主题。查看[演示](https://codepen.io/coryrylan/pen/eYOWryP)