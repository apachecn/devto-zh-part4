# 用 C 语言实现整数运算时溢出

> 原文：<https://dev.to/devmarchan/overflow-in-integer-arithmetic-with-c-4136>

## 溢出

有时候，处理整数的结果可能会超出可以表示的范围，也就是说，当对两个 k 位数进行运算的结果是(k+1)位的结果时。Polyspace 或 Sonar 等静态分析工具也可能触发操作警告，或者在最坏的情况下，您会在运行时遇到错误。

通常，加法和乘法是溢出的来源，但减法和 abs 函数也会出现溢出。

## 如何克服难题

### 向上铸造到更大的尺寸。

第一个建议要求知道您正在处理的类型的最大(或最小)值。如果是这样，有可能做这样的事情:

```
#include <stdint.h> 
int16_t add_without_overflow(int16_t x, int16_t y)
{
  int32_t z = (int32_t) x + y;

  if (z > INT16_MAX)
  {
        z = INT16_MAX;
  }
  else if (z < INT16_MIN)
  {
        z = INT16_MIN;
  }
  return (int16_t) z;
} 
```

这段代码将加法向上转换为更大的字体大小，从 int16 到 int32，从两个 k 位数到(k+1)位数。之后，检查溢出。也就是说，如果你的结果符合 k 位数，你就得到它了。

### 用来与一个已知的域值进行比较

也许不太容易知道目标平台中的最大值或最小值或某个类型。在这种情况下，一个简单的克服方法是用特定定义域的最大值或最小值来检查操作数。假设我们对两个变量进行操作，这两个变量代表以克为单位的两个负载的重量。但是我们保证(根据系统要求)任何负载都不能超过 5.000 克。

可以只放:

```
int16_t add_without_overflow(int16_t x, int16_t y)
{
  int16_t total = 0;

  if (x <= MAX_LOAD_WEIGHT &amp;&amp; y <= MAX_LOAD_WEIGHT)
  {
        z = x+y;
  }

  return total;
} 
```

此外，这样可以检查数据的正确性。

然而，在我看来，这种方法有一个缺点。你需要非常小心域名的范围。想象一下，负载的最大值可能是 40，000。增加两个最重的负载(40，000+40，000)会导致溢出。

在我看来，当你的域值远离你的字体大小的限制时，这将是一个有效的解决方案。

## 参考文献

这里可以找到更精确和详细的解释

[捕捉 C 中的溢出](https://www.fefe.de/intof.html)
[了解 C/C++中的整数溢出](http://www.cs.utah.edu/~regehr/papers/overflow12.pdf)

[迈拉姆的原始帖子](https://myram.xyz/overflow-in-integer-arithmetic-with-c/)