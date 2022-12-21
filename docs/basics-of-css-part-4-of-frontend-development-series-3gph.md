# CSS 基础-前端开发系列的第 4 部分

> 原文：<https://dev.to/dillionmegida/basics-of-css-part-4-of-frontend-development-series-3gph>

对于许多不知道的人来说，我已经写了一系列的前端开发，正在使用的指南是[开发者路线图——前端](https://roadmap.sh/frontend)

[查看这篇文章](https://dev.to/dillionmegida/frontend-development-series-3004)来跟进这个系列。

事不宜迟，我将进入我们今天的主题，

### CSS 基础知识

CSS 是`Cascading Style Sheets`的首字母缩写。
作为一名 web 开发人员，毫无疑问，你不可能没有遇到过这种情况。

这本身是一个非常广泛的主题，但我会尽最大努力用最简单的术语解释它。

众所周知，HTML 处理页面上的标记。它还使用标签为页面添加内容和含义。CSS，简单来说，`selectively adds styles to the contents on your page`。

> CSS 是一种样式表语言，用于通过控制内容的显示方式来增加网站的外观。

### 实现 CSS

CSS 可以用来设计页面上几乎所有元素的样式，这取决于元素拥有的属性。
在 CSS 中，我们有；

### -内部 CSS

这里，CSS 和 HTML 在同一个文件中使用。它们可以用在头部标签或身体标签中，但建议使用头部标签。

```
<html>
<head>
    CSS Basics
    <style>
        h1 {
            color: pink;
            font-size: 16px;
        }
    </style> 
</head>
<body>
    <h1>Frontend Development Series</h1>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这个程序的大部分对你来说应该不陌生，因为你现在应该熟悉 HTML 了。

让我们考虑一下程序的 CSS 部分

```
....
    <style>
        h1 {
            color: pink;
            font-size: 16px;
        }
    </style>
.... 
```

Enter fullscreen mode Exit fullscreen mode

这是 CSS 的主要语法。它涉及到选择器(在我们的例子中，它是`h1`)。
它选择当前 HTML 文件中所有的`<h1>`元素。

接下来，我们有宣言。声明分为两部分，即:`property`和`property value`

属性是颜色和字体大小，值分别是`pink`和 16px。颜色也可以是十六进制表示。

我们的程序在上面所做的是选择所有的`h1`元素，并对每一个元素应用粉红色和 16 像素的字体大小。

> HTML 中不同的元素拥有不同的属性。颜色属性并不存在于所有元素中。您需要了解元素中存在的属性，以便知道如何用 CSS 修改它。

### -内联 CSS

这种实现方式有点类似于内部 CSS。这里的不同之处在于样式应用于内嵌的元素。我什么意思？

```
....
<h1 style='color:pink; font-size: 16px'>Frontend Development Series</h1>
.... 
```

Enter fullscreen mode Exit fullscreen mode

我们上面的程序和这个程序呈现的是同样的东西。但是，我们在这里的风格是应用于元素内联(找不到更好的词，😜).

> 这里需要注意的另一点是，样式只适用于那一行的`h1`元素，而不适用于页面上的所有`h1`元素。

### -外部 CSS

这里，样式在一个单独的文件中被应用和声明，然后在需要它的 HTML 文件中被引用。

```
<!-- The html file by name, index.html -->
<html>
<head>
    CSS Basics
    <link rel='stylesheet' type='text/css' href='style.css'>
</head>
<body>
    <h1>Frontend Development Series</h1>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
/* The css file by name, style.css */
h1 {
    color: pink;
    font-size: 16px;
} 
```

Enter fullscreen mode Exit fullscreen mode

请密切关注我们 index.html 中的`<link>`标签。它有一个`rel`属性，指定两个链接文档之间的关系，这个值在样式表中给出。我们还有值为`type/css`的类型属性，用于将文件标识为`.css`文件。最后，href 属性指定了样式文件的位置。

> 记住，这取决于文件的位置。我使用 style.css 直接意味着 index.html 和 style.css 文件在同一个目录的同一层。

这基本上是 CSS 的基础。

你可以[在这里](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/CSS_basics)阅读更多内容。

参考的文章是一个关于基本 CSS 的很好的文档。你也可以参考那里的其他文章。我很想写更多关于 CSS 的东西，因为它是一个广泛且仍在扩展的话题，但那里的文章已经完成了大部分工作。

我希望我的这篇总结文章已经给了你关于 CSS 的基本概念😃

感谢阅读: )

我也会在我的 [Dillion Megida 个人资料@ Hashnode](https://dillion.hashnode.dev) 上分享这些系列文章

您可以查看我过去关于前端开发系列的文章

*   [面向初学者的网页可访问性——前端开发系列的第 3 部分](https://dev.to/dillionmegida/web-accessibility-for-beginners-part-3-of-frontend-development-series-3kp7)
*   [基础 SEO -前端开发系列第二部分](https://dev.to/dillionmegida/basic-seo-part-2-of-frontend-development-series-2i64)
*   [语义 HTML -前端开发系列第 1 部分](https://dev.to/dillionmegida/semantic-html-part-1-of-frontend-development-series-nlb)