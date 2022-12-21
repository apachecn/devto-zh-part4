# 如何以正确的方式处理语义

> 原文：<https://dev.to/bnevilleoneill/how-to-do-semantics-the-right-way-nal>

**Written by [Kristofer Selbekk](https://blog.logrocket.com/author/kristoferselbekk/)** ✏️

在任何一个给定的网站上，都有很多隐含的意思，对于一些用户来说很容易理解。网站上有大量的视觉线索和提示。比平常大的文本通常是标题，导航菜单看起来像某种方式，放大镜图标倾向于表示搜索功能。

没有这些视觉线索，这些隐含的关系大部分都会消失。我们可以让某人(或某物)给我们读内容——但是所有帮助我们理解重点、段落和一般内容结构的小的视觉线索都将丢失。

想要概念证明吗？转到你现在正在做的网站，点击`cmd+a`或`ctrl+a`复制“一切”，将文本粘贴到你喜欢的文本编辑器中。**欢迎来到没有语义的世界。**

幸运的是，网络可以描绘比这更多的意义。本文将向您展示为什么 web 上的语义很重要，HTML 中内置了什么样的语义元素，以及如何在您的应用程序中使用它们来提高可访问性、可用性、SEO 和利润。

## 语义到底是什么意思？

就单词而言，“语义”是一个棘手的问题。它是复数但也是单数，它的意思是“单词的意思”。在 web 上，语义通常指 HTML 元素提供的内在含义。

在语义构建的网页上，这些 HTML 元素用于将其内容放入某种结构中。一些内容应该占据中心位置，而另一些内容本质上更为次要。有些内容要强调，有些内容要以引用的方式呈现。HTML 为所有这些类型的内容提供了工具，还有更多。

## 你为什么要在乎

以这种方式构造的内容(我们称之为“语义正确”的方式)更容易被感兴趣的人消费和处理。这带来了一些巨大的优势:

### 提高了可达性

通过使用正确的语义标记来组织你的内容，你将使它更容易被所有用户使用。屏幕阅读器将能够以最有效的方式呈现内容，并允许用户比没有语义标记时更快地浏览你的站点。

### 提高可发现性(SEO)

搜索引擎爬虫用语义标记正确地索引你的内容要容易得多。通过使用正确的标签，您将为决定内容查找难易程度的底层排序算法提供有价值的提示。由于语义标记对可访问性更好，您的评分也会得到额外的提升！[💪](https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f4aa.png)

### 更容易解析

对你的内容感兴趣的可能不仅仅是屏幕阅读器和爬虫程序。有[价格聚合器](https://blog.datahut.co/how-popular-price-comparison-websites-grab-data/)、[阅读列表应用](https://www.nytimes.com/2018/07/03/technology/personaltech/safari-reader-mode.html)，分享功能，可能还有一千多种方式可以消费你的内容，让这个过程尽可能简单只会让你获得读者。

### 你的代码更容易阅读

通读源代码并不是一种很好的体验。幸运的是，HTML 不仅仅是普通的`<div />`和`<span />`标签！您可以将特定于导航的内容放在一个`<nav />`中，这是您在一个`<main />`标签中的主要内容部分，突然之间，您的代码变得更容易扫描了。这可能不会增加最终用户的价值——但是一个快乐的开发人员是有生产力的，对吗？

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## HTML 有什么好提供的？

正如我提到的，HTML 远不止`<div />` s 和`<span />`。事实上，有超过 100 种不同的元素！您可以在 [MDN 参考文献](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)中查看所有这些内容，但是它们可以大致分为三组——内容分段、内容语义和功能元素。

## 内容切片

一个网页通常包含几个内容部分——页眉、页脚、主要内容区域等等。HTML 提供了一些元素，可以让你表示这些不同的页面“地标”。

其中一些你应该记住的是:

*   [`<main />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/main) 包装了你页面的实际内容，或者说是你 app 的主要功能。简单地说，这是一个没有页眉、页脚和侧栏的网站
*   [`<header />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/header) 应该包裹你的页面或者 app 的主标题，也是任何其他组内容的“头”。它可以包含一篇博客文章的标题和内容类别，或者你的应用程序的徽标和主导航
*   [`<section />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/section) 是一个通用的内容容器，它应该包装你的站点或应用程序的一部分。这些元素通常也有一个标题。一个好的经验法则是，一个部分应该逻辑地出现在你的网站的轮廓中
*   [`<article />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/article) 几乎类似于`<section />`。主要区别在于，你应该在可以在另一个上下文中单独重用的内容周围使用`<article />`标签，而不是在主要内容周围使用**标签。示例可以是博客文章摘要、评论甚至是完整的小部件**
*   `<aside />`有两种不同的用法。如果你在一个`<article />`标签中使用它，它被理解为包含与文章密切相关的内容，比如术语表或解释框。如果在文章之外使用，它可以用来包装半相关的内容，比如侧边栏或相关链接列表
*   [`<footer />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/footer) 通常用于为一篇文章或网站本身提供版权或作者信息
*   [`<nav />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/nav) 包裹页面的主要导航部分。你的主站点导航、分页、下一篇文章/上一篇文章的特性都应该被包装在一个`<nav />`中

通过记住这七个要素以及何时使用它们，你可以为你的网站提供很多结构。如果你感兴趣，你可以在本文中阅读更多关于结构化内容的内容。

## 内容语义

还有另一组 HTML 标签，可以让你构建网站的实际内容，我称之为“内容语义”。这组元素让您可以为内容本身添加语义，从而可以从常规文本中辨别出引用和图像标题。

有很多这样的东西，如果你曾经使用过 HTML，你可能对其中的大部分都很熟悉。这里有几个你可能没怎么用过，但是知道它们是很重要的！

*   [`<blockquote />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/blockquote) 换行并允许您引用这些引号的来源。如果来源是一个 URL，您可以使用`cite`属性，否则，您可以使用`<cite />`标签来提供来源的出处
*   [`<figure />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/figure) 应该把你的图像、插图和数字(即图表、代码片段)包起来。通过提供一个`<figcaption />`,您还可以添加一个与图语义相关的标题
*   [`<dl />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dl) 代表“定义列表”，当你需要显示一个键-值关系时非常有用。产品元数据和术语表就是很好的例子

上面的这些都包装了一个内容块。你可以(也应该！)变得更细粒度，并为每个块提供进一步的语义含义。

*   [`<time />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/time) 在时间上包裹着特定的时期或地点。您可以使用`datetime`属性进一步指定它，该属性接受更详细的时间戳
*   `<em />`创建*强调*，这在你使用屏幕阅读器或语音助手解析文本时非常有用。如果需要，您甚至可以嵌套它们来提供额外的强调级别
*   [`<small />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/small) 包装您的“法律文本”和版权声明
*   用于解释你使用的缩写。很奇怪的是，`abbr`标签本身就是“缩写”的缩写，不过姑且不论。你可以用`title`属性解释这个缩写

对于极感兴趣的人来说，确实还有一些值得一看。你可以在这里找到完整的列表[。](https://developer.mozilla.org/en-US/docs/Web/HTML/Element#Text_content)

## 结论

语义网不仅仅是一个流行词。通过使用适当的 HTML 标签，你可以为屏幕阅读器、搜索爬虫和所有用户提供结构化的提示。

如果不记得什么时候用什么，就多查几次。 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)令人难以置信，这篇文章概述了你最常使用的那些。试着看看如果你在新的登陆页面中不使用`<div />`或`<span />`你能坚持多久！

我希望这篇文章能启发你给你的文档结构增加一些意义，让你深入到这种叫做 HTML 的奇妙语言中去。

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[如何正确处理语义](https://blog.logrocket.com/how-to-do-semantics-the-right-way/)首先出现在[博客](https://blog.logrocket.com)上。