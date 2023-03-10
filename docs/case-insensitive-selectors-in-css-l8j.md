# CSS 中不区分大小写的选择器

> 原文：<https://dev.to/alvaromontoro/case-insensitive-selectors-in-css-l8j>

世界上有两种计算机语言:区分大小写的和不区分大小写的。

...然后，还有 CSS。

从一开始，从 case 的角度来看，CSS 就是一种怪异的语言。根据定义，它是不区分大小写的，但实际上，它更像是一种“混合体”:属性和值是不区分大小写的，而选择器是区分大小写的(大多数情况下)。

让我们看一个例子。考虑到以下 HTML 代码:

```
<div id=”green”>This is green.</div>
<div id=”Green”>This is Green.</div>
<div id=”gReEn”>tHiS iS gReEn.</div> 
```

Enter fullscreen mode Exit fullscreen mode

并添加一些 CSS:

```
#green { color: green; }
#Green { cOlOr: Green; }
#greEn { color: green; } 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看这些 CSS 规则是如何一个一个地应用于 HTML 的:

*   第一个 CSS 规则(`#green`)匹配第一个 div，它将以绿色显示。
*   第二个规则(`#Green`)匹配第二个 div，并且，即使属性是以奇怪的情况编写的，它也将以绿色显示。(记住，属性和值是不区分大小写的)。
*   第三个规则(`#greEn`)不适用于任何`div` s，因为 id 与任何元素的 id 大小写都不匹配。

这是因为 [CSS 尊重文档语言](https://www.w3.org/TR/selectors-4/#case-sensitive)对于元素名称、属性名称和值的区分大小写，并且由于 HTML 对于大多数属性是区分大小写的，所以选择器以区分大小写的方式进行处理...

...所有这些都可能导致选择器部分区分大小写的混乱情况。这是我们使用属性选择器的时候。属性的名称不区分大小写，而属性值仍然区分大小写。

```
[data-type=”car”] { color: red; }
[data-Type=”car”] { color: blue; } 
```

Enter fullscreen mode Exit fullscreen mode

这两条规则将应用于具有属性`data-type`的元素。

* * *

所以，现在我们已经建立了 CSS 区分大小写和不区分大小写的情况，让我们看看随着 [CSS 选择器级别 4](https://www.w3.org/TR/selectors-4/) 的引入，特别是随着属性选择器的[区分大小写选项的引入，这种情况会有什么变化。](https://www.w3.org/TR/selectors-4/#attribute-case)

这个想法非常简单:只需**在值的末尾加上一个`i`，就在右括号** ( `]`)之前。如果你愿意，你甚至可以使用大写的`I`。

这将向浏览器表明，必须以不区分大小写的方式考虑属性值。所以大概是这样的:

```
[data-name="John" i] 
```

Enter fullscreen mode Exit fullscreen mode

将所有数据属性名称拼写为 john 的元素以不同的形式匹配:“jOhN”、“JOHN”、“John”、“John”等。

> 注意:如果你使用了一个`s`而不是一个`i`，那么字符串将根据大小写进行匹配。这对于少数实际上不区分大小写的 HTML 属性(比如列表中的“type”)可能很有用。

回到前面的 HTML 代码示例:

```
<div id=”green”>This is green.</div>
<div id=”Green”>This is Green.</div>
<div id=”gReEn”>tHiS iS gReEn.</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在，在属性选择器和区分大小写选项的帮助下，我们可以创建一个 CSS 规则来匹配所有的元素:

```
[id="green"i] { color: teal; } 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:属性选择器的特异性比 ID 选择器低，所以它的优先级较低。例如，如果我们将第一个例子中的 CSS 规则与最后一个结合起来，前两个`div`将是绿色的，只有最后一个是蓝绿色的，即使所有的元素都与规则匹配。

乍一看，不区分大小写选项似乎是为边缘情况(没有双关的意思)而不是为日常样式设计的。对于某些我们不知道属性大小写的情况，这可能很有趣，但是这种情况应该很少发生。另外，根据定义，这些模式/大小写匹配规则比较慢，所以最好少用。

不过，这种不区分大小写的选项是 CSS 工具箱中的一个不错的技巧。

* * *

这篇文章最初发表在阿尔瓦罗·蒙托罗的个人博客上。