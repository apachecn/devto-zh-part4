# 调试 Karmabug

> 原文：<https://dev.to/larsgw/debugging-the-karmabug-p3a>

*调试就像你刚刚跌下命运的命运之轮(图片由 [Cayambe - Own work，CC BY-SA 3.0](https://commons.wikimedia.org/w/index.php?curid=8860206) )*

由于某些原因，我需要一个新的库来在 Node.js 中进行同步 HTTP 请求。我知道你在说什么，“但这是 JavaScript 的七宗罪之一！”^1:嗯，你要知道我有我的理由，我想取代他。

由于我已经在我的库的异步部分使用了带有`node-fetch`的 Fetch API，我想:为什么不构建一个`sync-fetch`，像`sync-request`使用`(then-)request`一样使用`node-fetch`。两天后，据我所知，它真的起作用了。然而，如果我想出版这本书，我需要一些实际的测试。

幸运的是`node-fetch`有一个很好的测试套件，我只需要转换 **194 个**测试用例来使用同步 API。不是有趣的工作，但也许值得。不管怎样，第一个测试用例工作了，但是在第一个实际请求时就卡住了。

这就是我要向你介绍卡玛博格的地方。你看，经过一些测试，我发现*只有*我的*`sync-fetch`和测试服务器的组合...停了。论点是正确的，我的`fetch`与`https://example.org`一起工作，测试服务器与`node-fetch`一起工作，但是这个组合就是不行。调查一个指向另一个，我不知道下一步该怎么做。*

 *我想，这会是一条很好的推特。我想这是发出同步 HTTP 请求的报应差不多两分钟后，我突然明白了:这就是*到底发生了什么*。测试服务器无法响应请求，因为请求*本身*阻塞了事件循环。

^1 顺便说一下，JavaScript 的七宗罪都是懒惰。*