# CSS 显示属性

> 原文：<https://dev.to/phuchieu/css-display-property-4nai>

在[本帖](https://frontend.cloudaccess.host/css-property-position/)中，我们已经谈到了 css 最重要的属性:`positon`。除此之外，如果我们忘记提到另外一个 css 属性，那将是一个很大的错误，这个属性在我们的前端开发中也是非常重要的，那就是 css `display`。让我们跟我来谈谈吧

# CSS 显示的目的是什么

CSS 显示将定义你的 HTML 元素如何出现在视图中。你希望你的元素采用父元素的全部宽度，或者调整里面内容的宽度，甚至为你的元素定制大小，CSS 显示所有你想在那个场景中使用的内容。

# CSS 显示有哪些可用值

css 显示中有许多可用的值，例如:`block, inline, inline-block, flex, table ...`。在这篇文章中，我们将主要关注 3 个最重要的值:`block, inline and inline-block`。

## 什么是`display: block`

用 css `display:block`会让你的元素取父元素的全宽，不管里面的内容有多宽。当我们想让我们的元素独立存在，没有其他元素在它的左边或右边时，通常会用到它。因此，这个 css 将使我们的元素更容易与其他元素区分开来，也更容易被注意到。因此，`display:block`是以下元素的默认值:

`<div>
<h1> – <h6>
<p>
<form>
<header>
<footer>
<section>`
例如
[https://codepen.io/phuchieuct93abc/embed/NZxaae?height=600&default-tab=css,result&embed-version=2](https://codepen.io/phuchieuct93abc/embed/NZxaae?height=600&default-tab=css,result&embed-version=2)

正如您在上面的示例中看到的。每个块元素都显示在前一个同级元素的下面，不在左边，不在右边，只在底部。

这里有趣的一点是 div 元素。即使它定义了一个自定义宽度(不是完整的父宽度)，它仍然显示在整行中，因为浏览器将为 block 元素添加边距，如果它提供自定义大小的 css，以确保没有人可以与 block 元素坐在同一行。

请记住，对于块元素，css `margin-right`没有任何作用。除此之外，我们可以根据 css 宽度、高度、上边距、下边距和左边距自由定制大小块元素。

如果我们想让多个元素坐在同一行中，我们应该怎么做？这些场景，我们会用到`display:inline`。

## 什么是`display: inline`

不像`display:block`让一个元素在整个行中独立存在，`display:inline`让我们能够把多个元素放在同一行中。

`display:block`是以下元素的默认值:

`<span>
<a>
<img>`
[https://codepen.io/phuchieuct93abc/embed/OeMxar?height=600&default-tab=css,result&embed-version=2](https://codepen.io/phuchieuct93abc/embed/OeMxar?height=600&default-tab=css,result&embed-version=2)T3】

然而，`display:inline`将忽略我们的自定义大小(通过 css 宽度、高度和边距)。内联元素总是适合内部内容的大小，不多也不少。简单地说，除了改变内联元素内容的大小，你不能改变内联元素的大小。仅当您确定希望元素适合内容大小且完全不需要自定义大小时，才使用`display:inline`。

如果我们想将多个元素放在同一行中，并自定义大小，该怎么办？来到我们的手中

## 什么是`display: inline-block`

`display:inline-block`是`display:inline`(可以将多个元素放在同一行)和`display:block`(可以通过 css 宽度、高度、边距改变元素的大小)的组合。

首先，我们可以在同一行中放置多个内联块元素。接下来，我们可以自定义每个元素的大小，使其符合我们的需要。我们可以自由定义这些元素的宽度、高度和边距。
[https://codepen.io/phuchieuct93abc/embed/GboQMx?height=600&default-tab=css,result&embed-version=2](https://codepen.io/phuchieuct93abc/embed/GboQMx?height=600&default-tab=css,result&embed-version=2)

# 总结起来

与 CSS 并列的位置，显示在 CSS 中最重要的属性之一，帮助我们开发有吸引力的 web 应用程序。掌握 css 显示的唯一方法我们需要了解它的工作原理以及如何正确使用它。希望你学到了一些东西，下一篇文章再见。不要忘记把你的评论写在下面

你也可以访问我的博客，阅读更多有趣的话题:[https://frontend.cloudaccess.host/css-display-property/](https://frontend.cloudaccess.host/css-display-property/)