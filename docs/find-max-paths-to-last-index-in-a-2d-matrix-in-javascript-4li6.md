# 找到麦克斯。Javascript 中 2D 矩阵最后一个索引的路径

> 原文：<https://dev.to/dhilipkmr/find-max-paths-to-last-index-in-a-2d-matrix-in-javascript-4li6>

[![](img/8eaa01684f6ea0276886b0f163d4bd13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t2E9Eh1m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s54oqso213yaswh1rn9k.png)

在我的[网站](https://www.dhilipkmr.dev/blogs)阅读我所有的博客，在[推特](https://twitter.com/dhilipkmr_)关注我

距离我上次写博客已经有一段时间了。我将会写并解释一些算法以及我遇到的一些有趣的 Javascript/React 概念。

[![](img/34d3f3141ba2ea164e4b2d09825c171b.png)](https://i.giphy.com/media/aQYR1p8saOQla/giphy.gif)

### 问题陈述:

*   给定 2D 矩阵的行和列的长度。
*   从原点(0，0)开始，找出可以到达最后一个索引的最大路径数。
*   允许的移动方式= >向右和向下。

示例:

*   设，行的长度和列的长度是(2，2)
*   从(0，0)开始，应该到达(1，1)
*   所以路径的数量是:2

[![](img/f909b7b17a850f6d09c0d974b586c239.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1KJbR8Cz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x0blnznhz8vxg1coshtb.png)

路径一:(0，0) -> (0，1) -> (1，1)
路径二:(0，0) - > (1，0) - > (1，1)

## 思维过程:

### 检查图案:

*   对于每一个索引，我们被允许以向下和向右两种方式遍历。所以这是一种模式。

### 边缘病例:

*   当我们迭代时，我们需要小心结束索引的计数，所以我们应该处理这些情况。
*   当行或列的输入小于 1 时，Path 为 0。(即)行长度和列长度的输入不能小于 1

我们发现了一种模式，可以遍历我们可以选择用迭代/递归来解决的每个索引。

这里我们将通过递归求解！

[![](img/9775b0c5f8361881a04f2dd27d489281.png)](https://i.giphy.com/media/3oriOeBHxdF9FokwJq/giphy.gif)T3】

```
const num_of_paths = findMaxPathSrcToDes(3, 3);
console.log('Number of Paths', num_of_paths); 
```

我们调用`findMaxPathSrcToDes`并传递行长度和列长度并记录它。

### 中间函数:

```
function findMaxPathSrcToDes(rows, cols) {
  // Initial rows and columns to begin with.0,0 is the first row and col index we are choosing
  return findMaxPath(0, 0, rows - 1, cols - 1);
} 
```

*   `findMaxPathSrcToDes`函数接受来自用户的行长度和列长度。
*   然后它返回来自`findMaxPath`函数的输出，我们将原点(0，0)和目标索引(rows -1，cols - 1)传递给它。
*   通过接受这些，我们可以将起点和目的地索引修改为用户定义的位置，这样我们就可以从任何索引中识别任何索引的路径数。

## 寻找路径:

`findMaxPath`函数接受 4 个参数，输出路径号。

*   `currentRow` -表示当前正在处理的索引行。
*   `currentColumn` -表示正在处理的当前索引的列。
*   `destRow` -目的行索引
*   `destCol` -目的列索引

在任何递归解决方案中，从编写**基本条件或退出条件开始。**

那么，什么是基本条件或退出条件呢？

这基本上是满足我们的算法应该终止的情况。所以，让我们制定一个。

*   当`currentRow > destRow`时(在这种情况下，它表示`currentRow`计数已经超出界限)。
*   当`currentColumn > destCol`时(在这种情况下，它表示`currentColumn`计数已经超出界限)。所以在这两种情况下我们都返回 0。

```
function findMaxPath(currentRow, currentColumn, destRow, destCol) {
  // Base condition
  if (currentRow > destRow || currentColumn > destCol) {
    return 0;
  }
} 
```

## 成功案例:

*   如果`currentRow === destRow`或`currentColumn === destCol`表示我们已经到达目的地索引，那么我们返回`1`来表示成功的路径。

```
if (currentRow === destRow && currentColumn === destCol) {
     return 1;
  } 
```

## 递归案例:

*   对于每个索引，有两种方法 1。右二。向下
*   因此，我们必须在两个方向上递归，并添加从每个方向形成的路径。
*   我们通过将`currentRow`加 1 来调用`findMaxPath`。
*   然后再将`currentColumn`加 1，将两者的输出相加并返回。

```
const pathsInRows = findMaxPath(currentRow + 1, currentColumn, destRow, destCol);
const pathsInColums = findMaxPath(currentRow, currentColumn + 1, destRow, destCol);
return (pathsInRows + pathsInColums); 
```

CodePen 链接[此处](https://codepen.io/dhilipkmr/pen/dEExbQ?editors=0011)

下一步:

*   您可以尝试打印所有可能的路径以及 count。

[![](img/fe6cf766139fcfe240648062858827db.png)](https://i.giphy.com/media/mgqefqwSbToPe/giphy-downsized-large.gif)

关注我有趣的内容。

[回购链接](https://github.com/dhilipkmr/datastructures/blob/master/maxPath2DMatrix.js)
我的[网站](https://www.dhilipkmr.dev)，[博客](https://dev.to/dhilipkmr)和[推特](https://twitter.com/dhilipkmr_)

那都是乡亲们！