# 在 Python 中从十进制转换为二进制

> 原文：<https://dev.to/tonetheman/converting-from-decimals-to-binary-in-python-3n4d>

我开始听这个播客

[https://www.codenewbie.org/basecs/3](https://www.codenewbie.org/basecs/3)

到目前为止，如果你开始学习计算机科学，这似乎是一个很好的播客。或者只是想刷新一下。

听第一集的时候，我想知道...

在 Python 中，如何将十进制数转换成十六进制或二进制数？

```
a = 43
print(a)
# now binary print(bin(a))
# now hex print(hex(a)) 
```

Enter fullscreen mode Exit fullscreen mode

函数 bin 和 hex 将把一个数字转换成二进制和十六进制的字符串表示。