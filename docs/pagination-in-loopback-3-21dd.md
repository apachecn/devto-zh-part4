# 回送 3 中的分页

> 原文：<https://dev.to/mahnuh/pagination-in-loopback-3-21dd>

*本文原载于[媒体](https://medium.com/prototype-berlin/pagination-in-loopback-3-1cb5e7e7bc20)。*

当开发一个具有回送后端的应用程序时，通常不需要很长时间，直到您需要在前端进行某种分页，以使无数条目的长列表更便于用户导航。

幸运的是 LoopBack 有一个跳过过滤器和一个限制过滤器，允许我们只使用一些 url 参数来实现分页。您获取列表的第一个请求将包含

```
?filter={“skip”:0,”limit”:10} 
```

Enter fullscreen mode Exit fullscreen mode

下一个请求

```
?filter={“skip”:10,”limit”:10} 
```

Enter fullscreen mode Exit fullscreen mode

诸如此类。这个挺有用的！

然而，无论您是构建一个经典的分页页面，一个加载更多按钮，甚至是某种无限滚动，您可能想知道到底有多少页面，或者还有多少项需要获取。在这篇文章中，我将讨论两个选项来解决这个问题:X-Total-Count 头和 loopback-paginator 混合。

## X-总计-计数表头

正如标题所暗示的，一个解决方案是在你的回复中添加一个标题，告诉你总共有多少个条目。这可以很容易地用下面的引导脚本来完成，您只需将它放在`server/boot`目录中。