# JS 中的插入排序

> 原文：<https://dev.to/atila/insertion-sort-in-js-23o>

## 这是什么

实现排序算法很简单。简而言之，插入排序遍历数组两次。第一次迭代对应于目标位置，而第二次迭代用于与未来元素进行比较。每次当前项小于比较值时，它都被插入到当前位置，并且第一次迭代向前移动。

## 何时使用

对于大型数据集不是很有效，但是对于小型数据集却非常有效和强大。它可以通过不改变相等的一组键的顺序来避免混淆。

```
const insertionSort = (unsortedList: []) {

  // First iteration:
  // get the target position
  for (let i = 1; i < unsortedList.length; i++) {
    const currentItem = unsortedList[i]
    let j

    // Second iteration:
    // compare elements, assign each to new sorted position
    for (j = i; j > 0 && currentItem < unsortedList[j - 1]; j--) {
      unsortedList[j] = unsortedList[j - 1]
    }

    unsortedList[j] = currentItem
  }

  return unsortedList
} 
```

## 不要脸的塞

我有一个个人游乐场，我计划在那里用 JavaScript 实现一些计算机科学基础知识。具有良好的测试覆盖率，并对何时/为何使用每个概念有所了解。在基础知识库中查看我的[插入排序实现。](https://github.com/atilafassina/fundamentals/blob/master/src/insertionSort/index.js)