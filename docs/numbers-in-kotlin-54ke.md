# 科特林的数字

> 原文：<https://dev.to/jay_tillu/numbers-in-kotlin-54ke>

*   Kotlin 处理数字的方式接近 Java，但不完全相同。例如，数字没有扩大转换，在某些情况下文字会略有不同。

**数据类型的数字可以分为两类:**

1.  整数
2.  分数

## 整数

* * *

*   没有分数的数叫做整数。
*   *注意，kotlin 中的字符不是数字。*
*   记住对于整数 ***Int*** 是默认的。也就是说，如果你指定了一个整数，而没有指定它的类型，那么默认情况下它将被认为是一个 Int。

**科特林基本上有四种类型的整数。**

1.  字节— 8 位
2.  短— 16 位
3.  Int — 32 位
4.  Long — 64 位

#### 整数文字常数

整数值有以下几种文字常量。

*   小数— 123
*   多头以大写字母 L-123 L 标记
*   Hexadecimal — 0x0F
*   二进制文件— 0b00001011
*   **不支持八进制文字。**

## 小数

* * *

*   带精度点的数称为分数。

分数有两种类型:

1.  浮点型— 32 位
2.  双精度型— 64 位

*   Float 提供单精度 32 位浮点值。它提供小数点后 6 位数的精度。

*   记住对于小数 ***双精度*** 是默认的。也就是说，如果你在没有指定小数类型的情况下给它赋值，那么默认情况下它会被认为是双精度的。

*   Double 提供双精度 64 位浮点值。它提供小数点后 12 到 16 位的精度(可能会有所不同)。

#### 分数文字常数

*   分数值有以下几种文字常量。
*   Kotlin 还支持浮点数的传统表示法。
*   双打:123.5，123.5e10
*   浮动由 **F** 或 **f** 标记:123.5f，154.5F

## 数超类

* * *

*   想象一下你不知道用户会输入哪种数字的情况。许多语言没有解决这种情况的方法。除了科特林。

*   在 kotlin 中，您可以使用 Number 超类，并使变量成为数字类型。所以无论用户会输入哪种数字。您的变量既可以接受整数，也可以接受双精度。

```
fun main(args: Array<String>) {
    var a: Number = 45  // Works fine with number super class.
    var b: Number = 45.20
    print(a)
    print(b)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 数字文字中的下划线(从版本 1.1 开始)

* * *

*   您可以使用下划线使数字常量更具可读性:

```
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L etc. 
```

Enter fullscreen mode Exit fullscreen mode

伙计们，这就是科特林的数字。如果我错过了什么，请随时告诉我。

在那之前，继续编码，继续爱。在另一篇文章中向您介绍。

> 想和我联系吗？以下是链接。我很乐意成为你的朋友。😊
> [Twitter](https://twitter.com/jay_tillu)
> [脸书](https://www.facebook.com/jaytillu.1314/)
> [insta gram](https://www.instagram.com/jay.tillu/)
> [Medium](https://medium.com/jay-tillu)
> 或者直接发邮件给我[jayviveki13@gmail.com](mailto:jayviveki13@gmail.com)