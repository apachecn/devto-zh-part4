# JS 堆

> 原文：<https://dev.to/yongliang24/js-heaps-4748>

/* Heaps 是一种树。

二进制堆类似于二叉查找树，但规则不同。
每个节点最多可以有两个子节点。没有向左或向右的顺序，
只要它们比父节点小或大。

MaxBinaryHeaps-父节点总是比子节点大。

父节点总是比子节点小。

二进制堆使左右两边均匀，树总是紧凑的，而不是单侧的。

*/

/*使用数组或列表表示堆树

```
special rules: 
from a parent node, for any index in an array, the left child is placed in 2*index+1,
the right child is placed in 2*index+2.

to find the parent node from child nodes, use Math.floor((index -1)/2)

much like stack/queue, heap can be implemented with array 
```

*/