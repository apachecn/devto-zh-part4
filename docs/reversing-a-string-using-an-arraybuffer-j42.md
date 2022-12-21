# 使用 ArrayBuffer 反转字符串

> 原文：<https://dev.to/bugmagnet/reversing-a-string-using-an-arraybuffer-j42>

JavaScript 中还有多少种反转字符串的方法？我又在看[友好手册](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays)想到了另一个办法。

方法被结尾的`.map()`稍稍放下。我更希望有一种不那么单调的方式将 ArrayBuffer 变回普通文本。

另一个问题是，我们存储的是 uint 值，所以要为表情符号的混乱做好准备。

```
function Bruce_ByteBuffer(string) {
  let buffer = new ArrayBuffer(string.length);
  let int8View = new Uint8Array(buffer);
  for (let i = 0, j = string.length - 1; i < string.length; i++, j--) {
    int8View[i] = string.charCodeAt(j);
  }
  return Array.prototype.slice.call(int8View).map(function (item) {
    return String.fromCharCode(item)
  }).join("");
} 
```

Enter fullscreen mode Exit fullscreen mode

速度方面，使用我的[框架](https://dev.to/bugmagnet/testing-string-reversal-functions-8m6)，它处于中端较慢的一端。

```
Sarah_ForOf                 1997.262 ticks
Bruce_CharAt                3165.792 ticks
Sarah_SplitReverseJoin      3382.125 ticks
Bruce_Recursive2            3423.004 ticks
Theophanis_SplitFor         3765.074 ticks
Nathanael_SplitReverseJoin  3829.166 ticks
Bruce_Recursive1            3981.59 ticks
Sarah_Reduce                4272.548 ticks
Theophanis_SplitFor_Bruced  4310.981 ticks
Sarah_Recursive             4580.1 ticks
Bruce_ArrayApplyMap         6305.892 ticks
Bruce_ReverseGenerator      8994.98 ticks
Bruce_MapSortMap            11262.885 ticks
Bruce_ByteBuffer            15190.07 ticks
Bruce_CharAt2               17016.049 ticks
Bruce_IteratorReverse       103529.193 ticks
Bruce_RegReverse            582476.836 ticks 
```

Enter fullscreen mode Exit fullscreen mode