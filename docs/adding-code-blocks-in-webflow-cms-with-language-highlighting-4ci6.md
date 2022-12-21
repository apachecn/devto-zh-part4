# 使用语言突出显示在 Webflow CMS 中添加代码块

> 原文：<https://dev.to/sebastian_scholl/adding-code-blocks-in-webflow-cms-with-language-highlighting-4ci6>

最近， [8base](https://8base.com) 的团队决定将我们的博客从 Medium 迁移到 [Webflow](https://webflow.com) 。如果对我们围绕为什么要这样做的对话的文字记录进行词汇分析，主要的表现将包括 SEO 和相关的行话。尽管如此，我们的首席设计师还是花了很多时间在 Webflow 上为[建了一个漂亮的新博客](https://8base.com/blog)——还把所有的帖子都转移到了他们的 CMS 上。‍只有当所有的工作完成后，我们才意识到 Webflow CMS 不支持代码块🤦‍
‍
这对我们来说不是小事。由于我们正在为发布我们的新 8base 教程项目做准备(即将推出！)并且经常发布用代码演示您可以在 [8base](https://8base.com) 上做的所有令人惊叹的事情的博客，以行内和块的形式显示格式正确的代码是必须的。

希望 Webflow 在最近一轮也是最重要的一轮融资之后，能够给予 CMS 一些特别的关注。他们产品的其他方面要么是挑逗，要么是体现了牛逼。那么，为什么我们对 CMS 的预期会更低呢？‍:也就是说，我们必须同时想出另一个解决方案。此外，正如大多数问题一样，它们很少是经历它们的人所独有的！在互联网上快速搜索一下，你会发现 daily 上有一篇博文详细描述了同样的问题并提出了解决方案。

恕我直言，作者的解决方案并不适合我们使用。
‍

```
def publish(solution = "our own!")
    puts "So, we came up with #{solution}"
end 
```

## 定义自定义标签

本着解决方案本身的精神，我会让这个解释简短而甜蜜！
‍
首先，我们定义了两个简单的定制标签。这些标签是 ERB + HTML 风格的混搭，可以很容易地表示代码示例的开始和结束时间。最初，我们尝试使用 HTML 样式标签(`<>`)。然而，他们被逃脱，只是导致了一个更乱的脚本。
‍

*   **内嵌代码**

`{% c-line %}shell command --options-maybe{% c-line-end %}`。
‍

*   **代码块**

```
{% c-block language="js" %}
function logMessage(msg) {
    var preText = "New Message: "
    console.log(preText+msg)
}

logMessage("Multi-line code blocks are important.")
{% c-line-end %} 
```

### 添加高亮. js

许多解决方案建议使用 Prism 来突出语法。通读他们的文件，感觉有点矫枉过正。所以我们转而登陆了 [Highlight.js](https://highlightjs.org/) 。Highlight 的伟大之处在于，它允许您轻松地对代码进行样式化，以反映首选的文本编辑器主题。这意味着，如果你是 Sublime Text 3 或 Atom 的粉丝，可以使用编辑器主题突出显示代码示例。

因此，我们在博客文章模板`<head>...</head>`标签中添加了以下内容。脚本标签加载 Highlight.js 库，第一个链接标签导入所有支持语言的语法高亮 CSS 样式，而第二个标签导入 [Atom One Light 编辑器主题](https://highlightjs.org/static/demo/)。
‍

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/9.15.9/highlight.min.js"></script>
<link rel="stylesheet" type="text/css" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/9.15.9/languages/css.min.js">
<link rel="stylesheet" type="text/css" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/9.15.9/styles/atom-one-light.min.css"> 
```

### 写剧本

默认情况下，Webflow 需要 jQuery，并使其在全局可用。这使我们可以在我们选择添加到页面模板的任何自定义脚本中使用它。自然，同样的事情可以用普通的 JS 来完成。然而，使用 jQuery 感觉更整洁一些。
‍

```
/* Run when the page has loaded */
$(document).ready(function(event) {
  /* This formats all the inline code examples. */
  $('p:contains("c-line"), li:contains("c-line")').each(function(index, el) {
    $(el).html(el.innerText.replace(
      /{\%\s*c-line\s*\%}(.*?){\%\s*c-line-end\s*\%}/gi,
      '<code class="inline-code">$1</code>'
    ))
  })

  /* This formats all the code block examples. */
  $('p:contains("c-block"), li:contains("c-block")').each(function(index, el) {
    $(el).html(el.innerHTML.replace(
/{\%\s*c-block\s*language=[\"|\'](.+)[\"|\']\s*\%}(.*?)\{\%\s*c-block-end\s*\%}/gi,
      "<pre><code class=\"$1\">$2</code></pre>"
    ))
  })

  /* Run Highlight JS on every code block */
  $('pre code').each(function(index, codeBlock) {
    /* Remove first child <br> */
    codeBlock.removeChild(codeBlock.childNodes[0])
    hljs.highlightBlock(codeBlock)
  })
}); 
```

‍:所以，有几件重要的事情值得注意。
‍

1.  该脚本在发生`$(document).ready()`事件时执行。
2.  `$("HTML_TAG:contains('CODE_TAG')")`选择器返回包含指定标签的所有 DOM 元素。
3.  Regex 语句将自定义的开始和结束标记与正确的 HTML 标记相匹配，并保留语言属性(对于块)和所有内部文本。
4.  DOM 更新后，使用 Highlight.js `hljs.highlightBlock()`函数突出显示生成的代码块。‍:我们决定放弃在内嵌例子中使用语法高亮。相反，我们将一个定制的内联代码类添加到独立样式的模板中。此外，我们不喜欢添加到每个代码块中的额外空间(`<br>`标签)。这就是为什么我们增加了`codeBlock.removeChild(block.childNodes[0])`线。

### 总结

一旦 Webflow 更新他们的 CMS 以支持 Markdown 或本机代码块，我们很可能会放弃我们刚刚分享的这个解决方案。也就是说，我们对自己的可读性和可维护性非常满意！‍
欢迎你自己使用这个策略，并告诉我们它是如何运作的。
‍
‍
‍
‍