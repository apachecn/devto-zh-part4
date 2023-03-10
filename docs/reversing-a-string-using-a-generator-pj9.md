# 使用发电机反转弦

> 原文：<https://dev.to/bugmagnet/reversing-a-string-using-a-generator-pj9>

所以有这样一个人[米歇尔·亨德里克斯](https://dev.to/elmuerte)他[说](https://dev.to/elmuerte/comment/da9e)“我正在寻找下一个比我更胜一筹的人”，所以我在这里超越了我自己。

在这里，我们使用一个[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator#An_infinite_iterator)反向遍历字符串，并将每个字符传递给一个数组，该数组在最后得到`.join()` ed。

```
function Bruce_ReverseGenerator(string) {
  function* reverseGenerator(string) {
    let str = string;
    let index = str.length - 1;
    while (true)
      yield str.charAt(index--);
  }

  let result = [];
  const gen = reverseGenerator(string); 
  var ch;
  while (ch = gen.next().value) {
    result.push(ch);
  }
  return result.join("");
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，装饰运行在[测试框架](https://dev.to/bugmagnet/testing-string-reversal-functions-8m6)中，并给出了令人惊讶的好结果，即:

```
C:\TMP>timer.ly /TIMES:1000
Sarah_ForOf                 1502.905 ticks
Bruce_CharAt                2646.537 ticks
Sarah_SplitReverseJoin      2715.699 ticks
Bruce_Recursive2            2786.771 ticks
Nathanael_SplitReverseJoin  3104.762 ticks
Bruce_Recursive1            3112.511 ticks
Theophanis_SplitFor         3208.017 ticks
Sarah_Reduce                3227.539 ticks
Theophanis_SplitFor_Bruced  3706.581 ticks
Sarah_Recursive             3810.085 ticks
Bruce_ArrayApplyMap         5318.65 ticks
Bruce_ReverseGenerator      7354.585 ticks
Bruce_MapSortMap            9637.046 ticks
Bruce_CharAt2               13913.316 ticks
Bruce_RegReverse            490151.264 ticks 
```

Enter fullscreen mode Exit fullscreen mode