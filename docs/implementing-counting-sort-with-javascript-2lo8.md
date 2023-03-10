# 用 JavaScript 实现计数排序

> 原文：<https://dev.to/davidinoa/implementing-counting-sort-with-javascript-2lo8>

计数排序是一种算法，在某些情况下，允许您在线性时间内对数组进行排序。要使用计数排序，您的数组必须只包含整数，并且假设您知道最小值和最大值。

## 实现

[![counting sort algorithm](img/77b05366fc7a3e012a4e9895762ce2fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--76RHUeRv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dt5kywjt727u7uh77e6w.png)

## 击穿

(1)创建一个新的数组`counts`，在这个数组中你将记录一个整数在你的输入数组中出现了多少次。`counts`的长度将由输入数组中值的范围决定。`counts`中的每个索引将对应一个整数，这个特定索引中的每个值将告诉我们这个整数在我们的输入中出现了多少次。

[![code fragment](img/76421e19abd271d64d8ace01ae99124f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G67dgXGm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/napcn0riwemqz1lhvlpl.png)

(2)如果我们所有的整数都是正的，我们可以在对应于其值的索引`counts`中跟踪每个整数的计数(即`counts[3]`是我们保存整数`3`的计数的地方)。但是对于这个处理负数的算法，我们必须确定一个`offset`值来帮助我们将负数和正整数映射到`counts`中的一个索引。

[![code fragment](img/dfe4105a5646a3f6e7b130cbba4dfc13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ibic7HfZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s9y65xmav5imgkjmq6oh.png)

(3)通过迭代原始数组来填充`counts`。每遇到一个整数，我们就把它的计数加一。

[![code fragment](img/0ba7c9eefb0f6add58ddd6f2d3a75ea5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uee8rai1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/djbmli2ph0prt63o3bl6.png)

(4)初始化一个数组，以升序保存整数。

[![code fragment](img/2432fa7758e5a0c8f1563c65de389f6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m8DgC9Wz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w2ap0mlcwow2m2k6e1xd.png)

(5)遍历你的`min`和`max`之间的所有整数。在每次迭代中，确定当前`integer`的`count`。如果`count`是`0`，我们继续下一个。但是，如果`count`大于`0`，我们将该整数推送到`sortedIntegers`多次，如`count`所示。

[![code fragment](img/39dda898d8990ec5a0d95475c0d6e513.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R0UY8ZQr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6it7at1q4sm0zhozehb3.png)

(6)返回排序后的数组。

[![code fragment](img/c8f46af571de285bafbc92e90557d3f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sb8mf9P9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ddx9bd06t258l4rx240u.png)

## 复杂程度

在最坏的情况下，该算法的复杂度如下:

*   时间复杂度:`O(n + k)`，其中`n`是你的原始数组中的整数个数，`k`是你的`min`和`max`值的范围。
*   空间复杂度:`O(n + k)`

当`k`小于或等于`n`时，两个复杂度都降为`O(n)`。