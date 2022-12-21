# CSS:异步加载 CSS

> 原文：<https://dev.to/oieduardorabelo/css-carregando-css-de-forma-assincrona-299i>

## 提高性能的简便方法

为了提高网页的性能和恢复能力，我们可以做的最引人注目的事情之一是加载 CSS，而不会减慢网页的呈现速度。这是因为，默认情况下，浏览器同步加载外部 CSS-在下载和分析 CSS 时停止页面的所有呈现-并且两者都可能导致延迟。显然，在页面开始呈现之前，必须至少加载站点 CSS 的一部分，要立即为浏览器获取此初始 CSS，我们建议将其放在页面本身(“t0”inline CSS“”或 HTTP2 server-pushing”)。对于总容量较小的站点，这本身可能就足够了，但如果 CSS 较大(例如大于 15kb 到 20kb)，则此技术可以帮助提高性能并按优先级划分性能。拆分后，必须在后台异步加载不太重要的 CSS-a . k . a .在这篇文章里，我打算描述一下这几天最喜欢的做这件事的方法，这已经有好几年了！

有多种方法可以异步加载 CSS，但没有一种方法比您预期的更直观。与**脚本**元素不同，没有什么属性**【async】**或**【defender】**可以简单地应用于**【link】**元素，因此多年来我们一直保留着】但是，最近浏览器对其 CSS 加载行为进行了标准化，因此可能不再需要专门的脚本(如 loadCSS)来处理它们之间的微小差异。

# O Código

今天，通过稍微了解浏览器如何处理各种元素属性 **link** ，我们可以获得使用一小段 HTML 异步加载 CSS 的效果。以下是异步加载样式表的最简单方法:

```
<link 
  rel="stylesheet" 
  href="/path/to/my.css"
  media="print"
  onload="this.media='all'"
/> 
```

# 了解其部分

这一行 HTML 简明扼要，但不是很直观，所以让我们详细介绍一下这里正在发生的事情。

首先，元素**连结**的属性设定为**列印**。“打印”是一种媒体，(*媒体类型*，它说:“将此样式表中的规则应用于基于打印的媒体”，也就是说，当用户尝试打印页面时应用这些规则。坦率地说，我们希望我们的样式表应用于所有介质(尤其是屏幕，*屏幕*)，而不仅仅是打印，通过声明一种与当前环境不匹配的介质类型，我们可以获得有趣而有用的效果:浏览器将加载样式表而不会延迟呈现这是有用的，但这不是我们想要的。我们还希望 CSS 在加载时应用于屏幕环境。为此，我们可以在完成上传时使用属性 **onload** 将媒体类型设置为 **all** 。

# 【rel = preload】不能也这样做吗？

是的，同样的方式！在过去两年中，我们使用链接[rel = preload]【rel = style sheet】(而不是**【rel = style sheet】**)来获得类似于前一个模式(加载后切换属性**【rel】而不是属性【t】使用这种方法还是很好的，但是使用 **preload** 时有一些缺点需要考虑。首先，[浏览器支援 **preload** 尚不出色](https://caniuse.com/#feat=link-rel-preload)，因此，如果要依赖此属性寻找并套用图纸，则需要填写(例如[load CSS 提供](https://github.com/filamentgroup/loadCSS/#how-to-use-loadcss-recommended-example)更重要的是， **preload** 搜索文件太早，*早期加载，*优先级最高，*优先级最高，*可能会忽略其他重要下载，这可能是比您实际需要的优先级更高的优先级**

幸运的是，如果想要**【rel = preload】**提供的高优先级搜索(在支持它的浏览器中)，可以将其与上面的模式结合起来，如下所示:

```
<link 
  rel="preload" 
  href="/path/to/my.css" 
  as="style"
/>

<link 
  rel="stylesheet"
  href="/path/to/my.css"
  media="print"
  onload="this.media='all'"
/> 
```

鉴于上述代码的简单性和声明性，我们会选择此代码而不是 polyfill，因此打印介质切换方法再次是我们的首选。

# 为什么不使用假媒体类型属性呢？

您近年来一直在阅读我们关于此问题的文章，您可能记得，我们使用诸如**【仅 x】**等媒体属性值来达到与**【print】**相同的效果，提供了一种与任何环境都不匹配的值，如媒体类型]当浏览器遇到与任何环境都不匹配的媒体类型时，它们现在会以相同的方式处理它们—无论如何都会上载文件。尽管如此，一些浏览器团队正在开始考虑区分不匹配的媒体类型和无效(或浏览器无法识别的)媒体类型以及可能不会请求使用无效媒体类型的链接文件的“[”这会破坏许多现有的 CSS 加载实现，因此不太可能，但出于安全考虑，我们建议使用与当前环境不匹配的有效兼容类型，例如**【print】**。](https://bugs.chromium.org/p/chromium/issues/detail?id=977573)

# 你可能不需要 loadCSS

我们继续维护 loadCSS，并认为它在某些情况下很有用，例如，当您希望以编程方式查找 JavaScript 中的 CSS 文件时，例如:**【load CSS("/path/to/my . CSS ")**。如果您已经使用 loadCSS 或其**rel = preload**填充图案，则无需更改任何内容。在内部，我们使用本文所述的相同的力学。

但是，越来越多的我们发现，简单的 HTML 方法可能就是您所需要的一切。

**而且简单的话总的来说比较好。**

# 谢谢阅读

有问题吗？[把我们加到推特上！](https://twitter.com/filamentgroup)

* * *

# 克雷蒂托

*   [异步加载 CSS 的最简单方法](https://www.filamentgroup.com/lab/load-css-simpler/?utm_source=oieduardorabelo)，escrito origination e por[灯丝组](https://twitter.com/filamentgroup)