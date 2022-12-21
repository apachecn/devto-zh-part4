# 检查字典在 D 中有关键字

> 原文：<https://dev.to/jessekphillips/check-dictionary-has-key-in-d-h7d>

我就举一个字典添加的例子。

```
string[int][string] data;
if("hello"!in data)
    data["hello"] = [95: "value"]; 
```

Enter fullscreen mode Exit fullscreen mode

`in`运算符对数组不可用。预计时间复杂度为 O(1 ),这对于字典是可能的，但对于列表是不可能的。