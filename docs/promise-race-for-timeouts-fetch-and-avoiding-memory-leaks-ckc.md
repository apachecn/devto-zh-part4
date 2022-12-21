# Promise .争用超时、获取和避免内存泄漏

> 原文：<https://dev.to/kgrz/promise-race-for-timeouts-fetch-and-avoiding-memory-leaks-ckc>

当新的`AbortController`不可用时，使用`Promise.race`是获得`fetch`呼叫的取消/超时行为的流行选项。然而，有一些方法可以用这种技巧搬起石头砸自己的脚。我写了一个帖子，内容如下:

1 如何使用`Promise.race`来实现`fetch`呼叫
的超时 2👾错误的实现
3 ✅A 更安全的实现使用了 JavaScript 中的新特性，不会有 2 中的问题。

[https://kgrz.io/avoiding-memory-leaks-timing-out-fetch.html](https://kgrz.io/avoiding-memory-leaks-timing-out-fetch.html)