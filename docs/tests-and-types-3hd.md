# 测试和类型

> 原文：<https://dev.to/brendt/tests-and-types-3hd>

想象一个简单的函数:`rgbToHex`。
它带三个参数，0 到 255 之间的整数；并将其转换为十六进制字符串。

下面是这个函数在动态弱类型语言中的定义:

```
rgbToHex(red, green, blue) {
    // …
} 
```

我想我们都同意“程序正确性”是至关重要的。我们不想要任何错误，所以我们编写测试。

```
assert(rgbToHex(0, 0, 0) == '000000')

assert(rgbToHex(255, 255, 255) == 'ffffff')

assert(rgbToHex(238, 66, 244) == 'ee42f4') 
```

由于我们的测试，我们可以确保我们的实现如预期的那样工作。对吗？

嗯……我们实际上只测试了 16，777，216 种可能的颜色组合中的三种。但是人类的推理告诉我们，如果这三种情况都行得通，那么很可能都行得通。

如果我们传递 doubles 而不是 integers，会发生什么呢？

```
rgbToHex(1.5, 20.2, 100.1) 
```

或者超出允许范围的数字？

```
rgbToHex(-504, 305, -59) 
```

那`null`呢？

```
rgbToHex(null, null, null) 
```

还是弦乐？

```
rgbToHex("red", "green", "blue") 
```

还是错误的论点数量？

```
rgbToHex()

rgbToHex(1, 2)

rgbToHex(1, 2, 3, 4) 
```

还是以上几种的结合？

在相对确定我们的程序做了它需要做的事情之前，我可以很容易地想到我们需要测试的五种边缘情况。这是我们至少需要编写的八个测试——我相信如果有时间的话，您还可以编写一些其他的测试。

继续读下去[http://stitcher.io/blog/tests-and-types#read-on](http://stitcher.io/blog/tests-and-types#read-on)