# D 中包含在字符串中的字符串

> 原文：<https://dev.to/jessekphillips/string-contained-in-a-string-in-d-4po8>

这是处理字符串时的常见任务。它不如正则表达式强大，但在检查字符串时却是一个快速的胜利。部分字符串匹配，包含，是一个简单的概念，D 隐藏了一点。

```
import std.algorithm;

auto haystack = "some long name";
auto needle = "long";

if(!haystack.find(needle).empty)
    haystackHasNeedle(haystack); 
```

Enter fullscreen mode Exit fullscreen mode

在这个语境中`!`是否定，(不是)...空的。

`find`是一个通用函数，适用于任何范围。因此它可以用于数字和其他容器。类似于`count`和`filter`，可以指定更复杂的条件。

```
import std.algorithm;

auto haystack = [1, 2, 3, 4];

assert(haystack.find!(x => x > 2)
               .equal([3, 4])); 
```

Enter fullscreen mode Exit fullscreen mode

它实际上是在条件匹配的地方移动范围的起点，剩下的部分由用户操作。这种方法被带到了其他函数中，使得字符串和其他数据的导航非常一致。

```
import std.algorithm;

auto haystack = "some long name";

if(haystack.skipOver("some"))
    assert(haystack.startsWith(" long")); 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章会很长，因为我会因为所有可用的一般方法而结束几乎所有的标准算法和标准范围。

相反，我必须提到弦的一个重要方面。Unicode 并不简单，虽然许多带字符串的算法可以在这个层次上工作，但 unicode 有规范化，还有字素要考虑。

[https://dlang.org/phobos/std_uni.html](https://dlang.org/phobos/std_uni.html)

使用另一种语言似乎不会更好。我一般不会花太多心思在这上面，我认为 gragheme 的两个主要操作是字符计数和字符串反转。我通常可以忽略这个细节。