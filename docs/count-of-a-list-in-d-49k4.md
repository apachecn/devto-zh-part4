# D 中列表的计数

> 原文：<https://dev.to/jessekphillips/count-of-a-list-in-d-49k4>

在这种情况下，我想得到列表中元素的数量。对于 D 数组来说并不难，但是我的搜索任务是计算满足条件的元素的数量，所以我想我也应该包括这个。

```
auto arr = [1, 2, 3];
assert(arr.length == 3; 
```

Enter fullscreen mode Exit fullscreen mode

为了根据标准进行计数，d 中有许多选项

```
import std.algorithm;
auto arr = [1, 2, 3, 2];
assert(arr.count(2) == 2); 
```

Enter fullscreen mode Exit fullscreen mode

```
import std.algorithm;
auto arr = [1, 2, 3, 2];
assert(arr
       .filter!(x => x == 2)
       .count() == 2); 
```

Enter fullscreen mode Exit fullscreen mode

有趣的事实是，`filter`使用了提供更多动力的 lambda，但这种技术也适用于`count`。

我想指出的是，我们正在输入一种更通用的导航数据形式。公开范围的用户类型。这也超出了本文的范围。当您开始使用基于范围的算法时，导入 std.array 并调用`.array`来急切地评估并获得满足所有范围类型要求的数组。

[https://tour.dlang.org/tour/en/basics/ranges](https://tour.dlang.org/tour/en/basics/ranges)