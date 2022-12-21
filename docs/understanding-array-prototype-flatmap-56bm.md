# 了解 Array.prototype.flatMap

> 原文：<https://dev.to/laurieontech/understanding-array-prototype-flatmap-56bm>

上周我们讨论了 ES2019 中可用的新扁平方法。

本周，我们将深入探讨`flatMap`！

# 先说地图

在 JavaScript 数组中，a 有一个名为`map`的内置函数。它接受一个函数，并使用该函数对数组中的每一项进行操作。类似这样的。

```
let arr = [1, 2, 3]

let result = arr.map((item) => item * 2); 
```

Enter fullscreen mode Exit fullscreen mode

`result`是一个数组，其中来自`arr`的每一项都乘以 2。

```
[2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

# 使用地图的另一种方式

还可以使用`map`在每个数组元素内部创建一个数据结构！让我们看看这个例子。

```
let arr = [1, 2, 3]

let result = arr.map((item) => [item, item * 2]); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们的结果是这样的。

```
[[1, 2],[2, 4],[3, 6]] 
```

Enter fullscreen mode Exit fullscreen mode

想把这个压平是常有的事。

所以你有

```
let arr = [1, 2, 3]

let result = arr.map((item) => [item, item * 2]).flat();

[1, 2, 2, 4, 3, 6] 
```

Enter fullscreen mode Exit fullscreen mode

# 但不含 flatMap！

有了`flatMap`的加入，这变得更简单了！

```
let arr = [1, 2, 3]

let result = arr.flatMap((item) => [item, item * 2]);

[1, 2, 2, 4, 3, 6] 
```

Enter fullscreen mode Exit fullscreen mode

这是一回事。

# 重要

`flat`的默认参数是 1，而不是无穷大。这很重要，因为`flatMap`也是如此。让我们看一个例子。

```
let arr = [1, 2, 3]

let result = arr.flatMap((item) => [item, [item]]);

[ 1, [1], 2, [2], 3, [3] ] 
```

Enter fullscreen mode Exit fullscreen mode

数组没有完全展平，因为`flatMap`只展平了一级。

# 结论

对于这种流行的模式，flatMap 是一个很好的内置函数！检查一下，让我知道你的想法。