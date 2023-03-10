# 寻找算法时间复杂度的初学者指南

> 原文：<https://dev.to/merastackhouse/beginners-guide-to-finding-the-time-complexity-of-an-algorithm-3bp2>

# 什么是时间复杂度？

时间复杂度是一种比较算法执行所需抽象时间的方法。它不是指实际的时间或持续时间。它只对比较有用(并且在比较相同的离散元素时；并非所有算法都具有相同的元素)。

# 大 O 怎么算？

### 1。将代码分解成不同的部分

这里有一些我们应该认识的片段:

A 组:

*   赋值、语句、访问数组中的某个元素、比较
*   例子

```
array[i]
const b = 5
b > 4 
```

B 组:

*   运行 n 次的循环或递归
*   例子

```
for (const i = 0; i < n; i++) {
  // do something
} 
```

C 组:

*   合并运行 n 次的循环
*   例子

```
for (const i = 0; i < n; i++) {
  for (const j = 0; j < m; j++) {
    // do something
  }
} 
```

D 组:

*   其他决定我们迭代元素次数的逻辑
*   例子

```
while ( low <= high ) {
  mid = ( low + high ) / 2;
  if ( target < list[mid] )
    high = mid - 1;
  else if ( target > list[mid] )
    low = mid + 1;
  else break;
} 
```

### 2。决定执行每一部分需要多长时间

大多数代码可以被分解成以上几部分，我们可以评估每一部分，以了解这段代码将花费多少时间。

A 组是最分散的代码片段。这些具体行动的数量是我们正在计算的。索引访问中的每个比较、赋值或数组花费的时间都差不多，我们认为是 1 个时间单位。注意:对于某些不使用比较/索引读取的算法，有不同的动作被认为是基线。

B 组是一个简单的 if 语句。If 语句将遍历长度为 n 的整个数组或列表。对于每个元素，我们执行循环中的任何操作。

C 组是组合循环或递归。因为循环是嵌套的，所以我们在外部循环中执行 n 次操作，每次运行时，我们在内部循环中执行 n 次操作。n * n 或 n^2.

组 D 是将影响与输入大小的对数相关的时间的逻辑。在这个例子中，我们有一个 while 循环，它每次运行时都将输入大小减半。

你可以在代码中做很多事情，你只需要弄清楚这个逻辑如何影响你接触的元素的数量或者你在这些元素上运行的次数。

### 3。把它们都加起来

在你计算出每个元素的时间后，我们只需要把它们加起来，然后在上面打个 O()就可以了！

A 组都值 1 单位时间，所以我们就用 O(1)。

B 组取 n 个单位的时间，所以，你猜对了，它们是 O(n)。

c 组花费的时间是 b 组的 n 倍，这可以表示为 O(n^2).添加的迭代次数越多，指数就越大。

最后，d 组。在我们的例子中，我们看到 while 循环实际上在每次运行时将我们迭代的列表大小减半。这个运行时间将比运行整个列表少得多。除以潜在时间用 O(logn)表示。

### 4。不要为鸡毛蒜皮的事伤脑筋

大 O 的一个好处是它不关心细节。大 O 是 n 向无穷大移动时时间的比较，在那里某些元素变得微不足道。不用担心 O(1)部分，也不用担心常数。如果有更高阶的 n 值，那么你也可以去掉较小的 n 值。5n^2 + n + 10 成为 O(n^2).随着 n 变大，指数部分对整个表达式的数量级影响最大。

###### 来源

*   [https://www . geeks forgeeks . org/analysis-of-algorithms-set-4-analysis-of-loops/](https://www.geeksforgeeks.org/analysis-of-algorithms-set-4-analysis-of-loops/)
*   [http://mohalgorithmsorbit.blogspot.com/](http://mohalgorithmsorbit.blogspot.com/)
*   [https://stack overflow . com/questions/11032015/how-to-find-time-complexity-of-an-algorithm](https://stackoverflow.com/questions/11032015/how-to-find-time-complexity-of-an-algorithm)
*   [https://www . study tonight . com/data-structures/time-complexity-of-algorithms](https://www.studytonight.com/data-structures/time-complexity-of-algorithms)