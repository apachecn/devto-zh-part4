# 这个字母排序函数是如何工作的？

> 原文：<https://dev.to/daviddoes/how-does-this-alphabetical-sorting-function-work-5bg4>

我很确定我是根据我看到的其他人的写的这个函数。问题是，我正在回顾一些代码，我并不完全理解它是如何工作的。

我有一个按字母顺序排序的`Set`。这个集合被恰当地命名为`uniqueSet`。代码如下:

```
let sortedList = uniqueSet
  .sort((a, b) => {
    if (a < b) return -1;
    else if (a > b) return 1;
    return 0;
  }) 
```