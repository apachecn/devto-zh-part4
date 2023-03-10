# 语义 HTML -前端开发系列的第 1 部分。

> 原文：<https://dev.to/dillionmegida/semantic-html-part-1-of-frontend-development-series-nlb>

我将从这篇文章开始前端开发系列。

这篇文章最初是在我的博客@上分享的

在我继续之前，我建议您浏览一下前端开发的路线图- [路线图-前端](//roadmap.sh/frontend)

路线图列表上的第一个是 HTML，标签是:

*   学习基础知识
*   编写语义 HTML
*   基础搜索引擎优化
*   易接近

在语义 HTML 之前，我将简要介绍一下 HTML 的基础知识。

`HTML`是`Hypertext Markup Language`
的首字母缩写，它是创建网页的标准标记语言。
它基本上描述了你的网页。
它由控制内容如何呈现给浏览器的标签组成。标签不会显示在浏览器上，但格式化的内容会显示在浏览器上。

*标记*是 HTML 标签对其中的内容所做的事情，例如，一个文本内容被标记为下划线。

HTML 文件是包含短代码的文件，以`.html`扩展名保存。这使得文件可以被浏览器查看。浏览器进一步呈现文件并显示由所用标签格式化的内容。

*   注意:浏览器不显示使用的标签，而是显示内容。

HTML 主要是网页内容的结构和生产者。

一个基本的 HTML 文件可能包含:

```
<!DOCTYPE html>
<html>
  <head>
    Basic HTML file
  </head>
  <body>
    <h1>Our program</h1>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

`<!DOCTYPE>`定义文档的类型。多余的`html`将文档定义为 HTML5。这将有助于浏览器充分呈现它。
`<html>`是我们页面的根元素。
`<head>`包含与我们页面相关的元信息。他们在网页中不可见
``元素分配我们网页的标题。您可以在浏览器标签中找到它们。
`<body>`包含了我们页面中可见的东西
`<h1>`定义了一个大的文本标题——“我们的程序”

### HTML 标签

它们是插入在`less than - <`和`greater than - >`之间的元素名称。它们通常成对出现。在我们的节目中，我们注意到了`<h1>Our program</h1>`。
这里的`<h1>`标签成对出现，因为它也有`</h1>`。它们被称为`opening tag`和`closing tag`。

> 结束标记在元素前有一个正斜杠，表示“结束”。
> 也有不成对出现的标签，例如用于插入图像的`<img>`标签不需要结束标签。

[这篇文章](https://www.w3schools.com/html/html_intro.asp)包含了很多关于 HTML 的信息，不能放在这篇文章里。
请在继续这篇文章之前通读一遍(如果你没有任何 HTML 的先验知识)

如果你通读这篇文章，你应该会对`Semantic HTML`略知一二。

# 编写语义 HTML

语义 HTML 是赋予网页意义而不仅仅是表现的 HTML。

我什么意思？

> 不仅仅是为了呈现视觉效果(这也可能是吸引人的)，描述性的含义被附加到页面上。

通过在文档中添加语义标签，您可以提供关于该文档的附加信息，这有助于交流。它们让浏览器清楚地知道页面及其内容的含义。这种清晰性也与搜索引擎沟通，从而提高搜索引擎优化。

## 语义元素

这些都是有意义的元素。
例子有`<code>`、`<em>`、`<blockquote>`等

提供新的语义元素，为页面增添意义。
包括:
`<article>``<aside>``<details>``<figcaption>``<figure>``<footer>``<header>``<main>``<mark>``<nav>``<section>``<summary>``<time>`

我不会详细解释每一个，但我们会看几个。

在引入这些新的语义标签之前，大多数开发人员会做这样的事情；

```
...
<div class='navbar'>
    ...
</div>
<div class='content'>
    ...
    <div class='article-section'>
        ....
    </div>
    ...
</div>
... 
```

Enter fullscreen mode Exit fullscreen mode

有了足够的风格，你就能实现你想要的实际展示。但是，这对于你的页面来说没有什么意义。

有了这些新的语义元素，我们上面的代码可以转化为，

```
...
<nav>
    ...
</nav>
<main>
    ....
    <section>
        ....
    </section>
    ...
</main>
... 
```

Enter fullscreen mode Exit fullscreen mode

div 标签只是一个将页面分成几个部分的容器。仅此而已。
但是通过上面使用的`<nav>`和`<main>`标签，我们已经可以预测这些部分将包含什么。

##### 注意:你可以选择添加类，但至少，那些节现在有了意义。

就像我上面提到的，这些标签也有助于更快的 SEO(搜索引擎优化)，因为页面被很好地描述了。

这部分就这些了。这里可以阅读更多[。敬请关注第二节。](https://www.w3schools.com/html/html5_semantic_elements.asp)

谢谢: )