# 任意大整数

> 原文：<https://dev.to/gmartigny/arbitrary-large-integer-1j7h>

## 问题

在使用 JS 时，您可能会遇到数字编码的限制。最臭名昭著的例子是:

```
0.1 + 0.2 === 0.3 // => false 
```

Enter fullscreen mode Exit fullscreen mode

Javascript 使用 64 位内存对数字进行编码(64 位二进制数字)。因为我们想处理浮点数(非有限集)，所以现代语言不得不依赖奇怪的数学。我不打算在这里解释，因为我自己也不完全明白。但是你应该知道在有限的内存中存储无限精度的数字是不可能的。

```
Math.PI // => 3.141592653589793 "only" 16 digits long. 
```

Enter fullscreen mode Exit fullscreen mode

因为它对小数字不够精确，所以对大数字也是如此。一旦你高于`Number.MAX_SAFE_INTEGER`或`2^53 - 1`，你就会出错。

```
Number.MAX_SAFE_INTEGER     // => 9007199254740991
Number.MAX_SAFE_INTEGER + 2 // => 9007199254740992 (1 + 2 = 2 ?) 
```

Enter fullscreen mode Exit fullscreen mode

如果你的整数超过了`Number.MAX_VALUE`或`2^1024 - 1`，情况会变得更糟，因为 JS 会将其视为`Infinity`。

```
Number.MAX_VALUE // => 1.7976931348623157e+308
Number.MAX_VALUE * 2 // => Infinity
1.8e308 // => Infinity 
```

Enter fullscreen mode Exit fullscreen mode

## 解

最近 Javascript 有了新的数字表示: [BigInt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Objets_globaux/BigInt) 。它现在被[火狐(v68)、Chrome (v67)和 Edge (v76)](https://caniuse.com/#feat=bigint) 支持。这允许你写出你计算机内存所能处理的最大数字。

它非常容易使用，只需在您的数字末尾添加一个`n`字符。

```
99 ** 999 // => Infinity
99n ** 999n // => 436073206168265161501341703386462012231079860756206... 
```

Enter fullscreen mode Exit fullscreen mode

如果你不能添加`n`，比如当你使用一个函数的结果时，你可以把它包装在一个构造函数中。

```
BigInt(getNumberViewers()) * BigInt(getAverageWatchDuration()) 
```

Enter fullscreen mode Exit fullscreen mode

使用 BigInts 时需要记住一些事情。

首先，BigInts 与常规数字不兼容。所以，在做数学时，你需要把所有的操作数转换成一个或另一个。

```
const bigInt = 99n ** 999n;
bigInt * 10 // => TypeError: Cannot mix BigInt and other types 
```

Enter fullscreen mode Exit fullscreen mode

其次，BigInts 与`Math`不兼容。只允许基本的数学运算。

```
Math.max(10n, 20n) // => TypeError: Cannot convert a BigInt value to a number
10n < 20n // => true 
```

Enter fullscreen mode Exit fullscreen mode

第三，虽然支持除法，但它们会自动舍入到最接近 0 的整数。

```
19n / 10n // => 1n
-19n / 10n // => -1n 
```

Enter fullscreen mode Exit fullscreen mode

最后，你必须考虑到 BigInt 比原生数字消耗更多的资源。所以你应该只在处理不断增长的数字时使用它们，比如时间戳、用户操作...

* * *

我希望你喜欢这篇小文章。在右边的面板中寻找我的更多文章，并考虑在 [Github](https://github.com/GMartigny) 或 [Twitter](https://twitter.com/GMartigny) 上关注我。

下次再见，✌️和平