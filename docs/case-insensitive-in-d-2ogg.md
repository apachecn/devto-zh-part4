# D 中不区分大小写

> 原文：<https://dev.to/jessekphillips/case-insensitive-in-d-2ogg>

在我告诉你用天真的方法之前，让我从天真的方法开始。

```
import std.string;

assert("I be Big".toLower == "i be big"); 
```

Enter fullscreen mode Exit fullscreen mode

正如我上次提到的，unicode 并不简单。改变字符并不容易，这主要不是 unicode 的问题。一种语言中字符的大写/小写在另一种语言中会发生变化。这就是本地化发挥作用的地方。

我实际上不太擅长这方面，所以质疑我的建议。如果你不是在处理跨越语言界限的数据，那么这种改变是没有实际意义的。

我还没有在 D 中看到处理这个的解决方案。在这篇文章中，我只是简单地研究了一下。所以继续使用 toLower，就像有人向我推荐 Python 一样。