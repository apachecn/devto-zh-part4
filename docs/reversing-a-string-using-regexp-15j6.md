# 使用 RegExp 反转字符串

> 原文：<https://dev.to/bugmagnet/reversing-a-string-using-regexp-15j6>

就在你认为出去是安全的时候，这里有另一个关于反转字符串的例子:使用 RegExp 对象。

```
function Bruce_RegReverse(string) {
  let res = "";
  const re = /^(.)(.*$)/;
  while (string !== "") {
    const match = re.exec(string);
    if (null !== match) {
      res = match[1] + res;
      string = match[2];
    }
  }
  return res;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的命名反映了我已经把它放到了我的[测试框架](https://dev.to/bugmagnet/testing-string-reversal-functions-8m6)中。结果表明，您不应该使用 RegExp 来反转字符串，或者至少不应该像上面这样:在一次运行中，萨拉奇马的 Sarah_SplitReverseJoin 平均花费了 2551.8 个节拍，Bruce_RegReverse 平均花费了 500494.9 个节拍。