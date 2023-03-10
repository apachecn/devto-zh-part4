# 一个简单的 bookmarklet 来发布当前页面

> 原文：<https://dev.to/codepo8/a-simple-bookmarklet-to-tweet-the-current-page-1mlk>

我喜欢在推特上发布我阅读的页面，格式如下:

> “标题”
> 
> [https://example.com](https://example.com)

大多数“分享这个”按钮会添加很多垃圾，如果没有，就必须先复制文本，然后将 URL 复制到 Twitter，这很烦人。

使用这个书签工具，你可以高亮显示一些文本，点击书签工具，在一个新的标签页中得到一个 tweet 窗口:

```
javascript:(function(){
  n=getSelection().anchorNode;
  t=n.nodeType===3?n.data:n.innerText;t='“'+t+'”\\n\\n';
  window.open(`https://twitter.com/intent/tweet?
  text=${encodeURIComponent(t)}${document.location.href}`)
})() 
```

Enter fullscreen mode Exit fullscreen mode

*说明:*

*   我们将整个事情包装在一个[life](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)中，以避免浏览器重定向到“javascript://”
*   我们得到当前`Selection`对象的`anchorNode`
*   我们检查类型。如果是文本节点，我们读取`data`，如果是 HTML，我们得到`innerText`
*   我们打开一个窗口(现在会出现一个新的标签页),调用 Twitter intend，文本后跟两个换行符和文档的 URL。

就是这样。

下面是添加到你的书签的缩小版本:

```
javascript:(function(){n=getSelection().anchorNode;t=n.nodeType===3?n.data:n.innerText;t='“'+t+'”\\n\\n';window.open(`https://twitter.com/intent/tweet?text=${encodeURIComponent(t)}${document.location.href}`)})() 
```

Enter fullscreen mode Exit fullscreen mode

复制这个，转到你的书签工具栏(如果它不可见，则在视图中改变)，并将位置设置为上面的代码:

[![Bookmark settings](img/ebc9e2453f67fdfe488b286fa0ab2498.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fynSV-dx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/08bpx7hqtjo0ullmz1q8.png)

或者，[转到我的博客上的文章](https://christianheilmann.com/2019/08/23/tweet-this-page-bookmarklet/)(作为开发者禁止 javascript: links)并将“tweet this”链接拖到你的工具栏上。

瞧，快乐的推特。