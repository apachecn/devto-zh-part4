# 当你索引一个空字符串时会发生什么？

> 原文：<https://dev.to/turnerj/what-happens-when-you-indexof-an-empty-string-1m0c>

看起来是一个相当直接的方法，它寻找一个字符(或字符集)的索引。字符串)并告诉您它出现的位置的索引。如果你寻找某个不存在的东西的索引，它通常会返回`-1`。(通常是因为 PHP 和它们的 [`strpos`方法](https://www.php.net/manual/en/function.strpos.php)返回`false`代替)

如果我们使用空字符串，你认为会发生什么？

```
//C#
"Hello World!".IndexOf(""); 

//JavaScript
"Hello World!".indexOf(""); 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看你是否猜对了...

。

。

。

事实证明，如果我们这样做，我们会得到`0`。

## 呃，什么？

现在。微软确实对此做了一些解释:

> 字符集包括可忽略的字符，这些字符是在执行区分语言或区域性的比较时不考虑的字符。在区分区域性的搜索中，如果 value 包含可忽略的字符，则结果等同于删除该字符的搜索。如果 value 仅包含一个或多个可忽略的字符，IndexOf(String)方法将始终返回 0(零)以指示在当前实例的开头找到匹配项。

这个解释似乎并没有解释*为什么*会希望它返回空字符串的`0`,事实就是如此。

对于 JavaScript， [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/indexOf) 简单地提到了这一点，但实际上并没有解释原因。更令人困惑的是，从 MDN 页面链接到的 ECMAScript 标准甚至没有提到它，而 Chrome、Firefox、Internet Explorer 和 Edge 却表现出这种行为。

我在 Twitter 上问了这个问题，蜜桃红提到了这种行为方式可能存在的问题。

> ![Mateus Canello Ottoni profile image](img/fb206d75149015ef0452a3a1189c3f2c.png)蜜桃红·卡内洛·奥托尼[@马特卡内洛](https://dev.to/mattcanello)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ MrTurnerj](https://twitter.com/MrTurnerj)我觉得就是奇怪。我期望的答案是-1。实际上，charAt(0)不会返回空字符串。因此，这种行为可能会导致不一致的状态。2019 年 8 月 17 日 12 点 21 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1162700906703593473)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1162700906703593473)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1162700906703593473)

虽然查找某个值的索引然后比较来自`charAt(0)`的值的情况可能不太常见，但它显示了查找索引的行为会导致奇怪的结果。

对我来说，查找一个空字符串的索引更像是一个“被零除”类型的问题。C#和 JavaScript(可能其他语言也是)这样做，一定有合理的原因。

你对它有什么想法——它应该返回`-1`、`0`还是抛出一个异常？

你知道它为什么会这样吗——让我在下面知道！