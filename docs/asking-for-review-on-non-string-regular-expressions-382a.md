# 请求复习非字符串正则表达式

> 原文：<https://dev.to/xowap/asking-for-review-on-non-string-regular-expressions-382a>

我有一个想法，我想推进关于“非字符串正则表达式”的讨论。我尽我所能解释了回购的事情。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ Xowap ](https://github.com/Xowap) / [ nsre](https://github.com/Xowap/nsre)

### 非字符串正则表达式

<article class="markdown-body entry-content container-lg" itemprop="text">

# 非字符串正则表达式

[![Build Status](img/3c1d9e440d494aa6880a347aae7c6351.png)](https://travis-ci.org/Xowap/nsre)

NSRE(非字符串正则表达式)是正则表达式的一个新发展，它真的很抽象，甚至与你所知道的正则表达式相比也是如此，但它在某些用途上也非常强大。

这里有一个转折:如果正则表达式不仅仅匹配字符串，还能匹配任何东西的任何序列，那会怎么样？

```
from nsre import *

re = RegExp.from_ast(seq('hello, ') + (seq('foo') | seq('bar')))
assert re.match('hello, foo')
```

Enter fullscreen mode Exit fullscreen mode

这里的主要目标是当一个单词有几种可能的解释时，匹配 NLU 语法，但是还有许多其他事情可以做。你只需要理解什么是 NSRE，并把它应用到一些事情上。

> **注意** —这是受[这篇文章](https://swtch.com/~rsc/regexp/regexp1.html)的启发，这篇文章来自 Russ Cox，解释了汤普森·NFA 是如何工作的，除了我…

</article>

[View on GitHub](https://github.com/Xowap/nsre)

我在寻找各种反馈

*   你明白这是什么吗？
*   你看到这方面的应用了吗？
*   API 好看吗？
*   你希望在这周围看到什么特征？
*   在生产中使用它之前，您需要什么？

谢谢！