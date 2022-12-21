# D 调中我的弦去了哪里

> 原文：<https://dev.to/jessekphillips/where-did-my-string-go-in-d-1ond>

我没有在这篇文章中搜索 Python，在我的上一篇文章之后，我认为在字符串中使用 std.algorithms 时调用意外行为是很重要的。

还记得 grapheme 注意，在字符串的原始范围设计中，人们认为 32 位 UTF 字符是一个完整的视觉字符。事实并非如此。

这导致从`char[]`到 dchar 的范围。虽然这有助于继续算法，但通常到最后你会再次需要一个字符串。

```
import std.range;
import std.conv;

string s = "foo".take(2).to!string; 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，我在本教程中给出的例子都是在我的手机上编写的，没有编译器。一般来说，应该可以将它们复制到 main 中，这样就可以工作了，除非调用未实现的函数。抱歉，如果他们没有编译。

```
void main () {
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://run.dlang.io/](https://run.dlang.io/)