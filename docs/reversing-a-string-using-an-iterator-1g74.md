# 使用迭代器反转字符串

> 原文：<https://dev.to/bugmagnet/reversing-a-string-using-an-iterator-1g74>

RTFM 的问题是有时候你会发现有趣的东西。所以我通读了 MDN 上的 [String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/@@iterator) 对象的文档，并发现了如何实例化一个迭代器。

```
function Bruce_IteratorReverse(string) {
  let iterator = string[Symbol.iterator]();
  let theChar = iterator.next();

  let result = [];
  while (!theChar.done) {
    result.unshift(theChar.value);
    theChar = iterator.next();
  }
  return result.join("");
} 
```

Enter fullscreen mode Exit fullscreen mode

将它放在[测试框架](https://dev.to/bugmagnet/testing-string-reversal-functions-8m6)中，运行 1000 次迭代，得出 C#秒表计时的平均速度。不像 RegExp 那么差，但是在末端，即:

```
Sarah_ForOf                 1954.52 ticks
Sarah_SplitReverseJoin      2628.535 ticks
Bruce_CharAt                2835.333 ticks
Theophanis_SplitFor         3088.03 ticks
Bruce_Recursive1            3442.696 ticks
Sarah_Reduce                3515.563 ticks
Bruce_Recursive2            3616.804 ticks
Nathanael_SplitReverseJoin  3751.542 ticks
Theophanis_SplitFor_Bruced  3815.779 ticks
Sarah_Recursive             4024.06 ticks
Bruce_ArrayApplyMap         5590.934 ticks
Bruce_ReverseGenerator      8441.915 ticks
Bruce_MapSortMap            10974.299 ticks
Bruce_CharAt2               14908.46 ticks
Bruce_IteratorReverse       93875.974 ticks
Bruce_RegReverse            524215.91 ticks 
```

Enter fullscreen mode Exit fullscreen mode