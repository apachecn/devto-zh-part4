# CSS 媒体查询-前端开发系列的第 6 部分

> 原文：<https://dev.to/dillionmegida/css-media-queries-part-6-of-frontend-development-series-2ep1>

根据我们的指引-[roadmap.sh/frontend](https://roadmap.sh/frontend)，前端开发系列的下一个环节是 **CSS 媒体提问**。
查看[前端开发系列文章的完整列表](https://dev.to/dillionmegida/frontend-development-series-3004)

目录

*   媒体查询简介
*   响应式网页设计
*   语法和示例

# [T1】简介](#intro)

CSS 中的媒体查询是什么？

媒体查询是 CSS 技术(在 CSS3 中引入),它指定当满足给定条件时更优先的样式，即

> **当条件为真时，查询中设置的所有属性和值首先考虑**

它使用包含样式声明的`@media`规则。

条件和样式也可以应用于特定的媒体类型。

一些媒体类型是:
`all` -这里声明的条件和样式适用于所有媒体类型。
`print` -此处的声明仅适用于打印模式。这定义了页面在打印预览和打印文档中的外观。
`screen` -在此处进行的申报适用于网站窗口。

媒体查询的一个主要好处是

# 响应式网页设计

用户在浏览网站时使用不同的媒介。可能是台式机，笔记本电脑，平板电脑，大尺寸手机，甚至小尺寸手机。

一个用户不应该因为使用的媒介而被剥夺你的网站的感觉。而且，你也不希望你的用户在手机上左右滑动，因为你的网页只是为大屏幕设计的。

响应式网页设计包括使用 HTML 和 CSS 来决定在不同的屏幕尺寸下什么应该隐藏、显示、缩小、放大等等。它们定义了当屏幕尺寸改变时，网站应该如何显示。

# 语法及示例

媒体查询最常见的用例是针对`width`和`height`媒体功能。[查看更多功能](https://www.quackit.com/css/css_media_features.cfm)

下面的例子将集中在`width`媒体特征、`screen`媒体类型和`and/only`逻辑运算符上。[查看更多逻辑运算符](https://css-tricks.com/logic-in-media-queries/)

### 语法

媒体查询的语法是:

```
/* .css file */
@media [logical operators and media types] ([media feature]: [value]) {
  selector {
    property: value;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
<!-- .html file -->
<body>
  <div>
    <h1>Frontend Development Series</h1>
  <div>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

```
/* .css file */
h1 {
  font-size: 50px;
  color: pink;
}
div {
  width: 80%;
  height: 70%;
  background-color: black;
}

@media only screen and (max-width: 500px) {
  h1 {
    font-size: 20px;
  }
  div {
    width: 100%;
    background-color: purple;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们上面的代码所做的是:

*   默认情况下，
    *   `div`元素的高度是父元素的 80%，宽度是父元素的 70%(这里的父元素是整个屏幕(`body`元素))，背景颜色是黑色
    *   `h1`元素的大小为 50px，颜色为粉红色。
*   宽度为 500 像素或以下(最大宽度:500 像素)
    *   `div`元素拥有 100%的新宽度和紫色的背景色。高度不变。优先级仅给予宽度和背景颜色属性。
    *   `h1`元素拥有 20px 的新字体大小，但是它的颜色保持不变。

结果:
[https://codepen.io/Dillion/embed/ExYOdOr?height=600&default-tab=css,result&embed-version=2](https://codepen.io/Dillion/embed/ExYOdOr?height=600&default-tab=css,result&embed-version=2)

您可以调整数码笔的缩放级别(1x、0.5x、0.25x)来查看变化。您还可以使用属性和值。

许多开发人员在考虑大屏幕之前先考虑小屏幕
，也就是说，为小屏幕声明样式，然后为大屏幕使用类似于`(min-width: 300px)`的条件。对于响应式网页设计，这是最值得推荐的方法，但是我建议你使用看起来对你方便的方法。

这是媒体查询的一个基本应用。有复杂的应用，但有了这种理解，你可以在响应式设计领域探索，也可以掌握媒体查询的使用。

有关媒体查询的深入知识，请查看本文- [使用媒体查询| MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries#Targeting_media_types)

欢迎提问或在评论区发表意见，或者在 [twitter](https://twitter.com/iamdillion) 上联系我。

我也在我的个人网站-[dillionmegida.com](https://dillionmegida.com)上写关于前端 web 开发的文章。