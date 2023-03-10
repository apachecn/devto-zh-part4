# JS 中的二分搜索法

> 原文：<https://dev.to/atila/binary-search-in-js-2l0j>

二分搜索法是一种非常强大的算法，可以在任何规模的数据集上查找结果。尽管如此，伴随这种性能而来的还有它正常工作的一个重要要求:列表**必须**被排序。另一个基本要求是知道列表中的条目数量，但在 JavaScript-land 中这是已知的。

### 工作原理

该机制实际上非常简单:将`array`分成两半，这导致程序具有 2 个范围的排序值(已经是初始集合大小的一半)。现在，程序检查目标值属于哪个值域。然后，它会再次执行，直到搜索到的值位于`array`的两端。

### 代码

让我们看看如何在 Typescript 中实现二分搜索法:

*   `list`:排序后的`array`。
*   `value`:待求的目标值。
*   `start`:当前迭代开始的`index`。
*   `stop`:当前迭代终止的`index`。

*   `@return`:找到的目标的索引

```
const binarySearch = (
  list: number[],
  value: number,
  start = 0,
  stop: number
):number {
  // `start` can be defined, but will default to `0`
  // as it is the first defined value of any array

  // if undefined, `stop` becomes the index of the last item
  stop = stop || list.length - 1
  // `middle` is the mean between `start` and `stop`
  const middle = Math.floor((start + stop) / 2)

  // if middle is not the target value
  if (list[middle] === value) return middle
  if (value < list[middle]) {

    // defines the `stop` value for the next iteration
    stop = middle - 1
  } else {

    // defines the `start` value for the next iteration
    start = middle + 1
  }

  // binarySearch calls itself again with the newly set values
  return binarySearch(list, value, start, stop)
} 
```

*感谢 Curtis Fenner 的改进建议！*

## 不要脸的塞

我有一个个人游乐场，我计划在那里用 JavaScript 实现一些计算机科学基础知识。具有良好的测试覆盖率，并对何时/为何使用每个概念有所了解。在基础库查看我的[二分搜索法实现。](https://github.com/atilafassina/fundamentals/blob/master/src/binarySearch/index.js)