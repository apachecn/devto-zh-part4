# 展开矩阵演练- JS

> 原文：<https://dev.to/knheidorn/unrolling-matrix-walkthrough-js-2d52>

在我开始找工作的时候，我一直在琢磨 LeetCode 的问题，尽我所能处理所有的算法练习，以缓解我参加技术面试时的紧张情绪。我一直在用 JavaScript 编写代码，并且一直在尽最大努力掌握“矩阵”问题。下面是我以*展开*的方式遍历矩阵的步骤。

### 问题

输入:[[1，2，3，4]，[5，6，7，8]，[9，10，11，12]，[13，14，15，16]]
假设输入永远是一个偶矩阵，(即 2×2，3×3，6×6 等。)

输出:“1，2，3，4，8，12，16，15，14，13，9，5，6，7，11，10”

### 蛮力选项和大 O

对我来说，第一步是始终遵循我的第一直觉，让蛮力选项发挥作用。我从以前的项目中了解到，我已经使用两个嵌套的`for-loops`来遍历每个单元格，构建了一个网格(又名矩阵)。虽然这显然不是最好的解决方案(运行时:O(n^2))，但我知道这是解决它的第一个方法。如果运行时 Big O 是 O(n ),我可能会继续走这条路，但是我想尝试用一个更独特的解决方案来解决这个问题。

### 重构代码

现在，我还没有真正为上面的*蛮力*选项写任何代码，所以这个标题有点骗人，但是如果我在一个真正的技术面试中，我肯定会想重构我的*蛮力*代码。相反，由于这只是实践，我决定真正分析问题。代码希望我以相同的顺序返回整个第一行，中间行的最后一个索引，以相反的顺序返回整个最后一行，最后是中间行的第一个索引。如果我要增加原始矩阵并删除上面的值，我将得到一个内部矩阵，该矩阵的值全部减少了 2。对于上面的输入示例，经过必要的删除后，4 x 4 矩阵将留给我一个 2 x 2 矩阵。这个新的矩阵需要以类似的方式处理。从本质上说，我无意中发现了一个很棒的递归函数练习。这仍然会导致 O(n^2)运行时，但至少从挑战的角度来看更有趣。

### 分手的步骤

我有一个总体的游戏计划，但决定开始编写我的解决方案的基本框架。

我知道对于递归函数，我需要从一个`base case`开始，也就是一个告诉我的函数停止的 case，以及一个调用函数本身的 ending function。此外，我知道输出不是数组，而是字符串。因此，我需要一个最初为空字符串的`ending`变量。鉴于这是一个递归函数，字符串也需要是一个参数，我将传递给函数，但将有一个初始值`“”`。

```
function unroll(matrix, endResult = “”) {
    //base case
    if (matrix.length === 0) {
      return endResult
    }

    //call itself
    return unroll(matrix, endResult)
} 
```

不错，但是我很快意识到我错过了关键的细节，链接我的递归字符串的`,`！

这就产生了新的代码:

```
function unroll(matrix, endResult = “”) {
    //base case
    if (matrix.length === 0) {
      return endResult
    }

    //call itself
    return unroll(matrix, endResult + “, ”)
} 
```

现在是时候开始遍历矩阵了。再说一次，我知道我应该把它分成一个个步骤。第一部分是按原样打印第一行。假设这是一个嵌套数组，我可以写`matrix[0]`或者`matrix.shift()`。就我个人而言，我想在白板问题中更舒适地使用方法，所以我选择了`matrix.shift()`选项。现在，为了将这个移除的数组转换成一个字符串，我使用了`.join(“, ”)`，记住我需要添加正确的标点符号。

```
function unroll(matrix, endResult = “”) {
    //base case
    if (matrix.length === 0) {
      return endResult
    }

    //top
    endResult += (matrix.shift().join(“, ”))

    //call itself
    return unroll(matrix, endResult + “, ”)
} 
```

下一步是获取最后两个元素，或者说右边的元素，因为**只有中间几行是**的。这将需要一个`for-loop`来遍历这些嵌套数组。没有它我无法解决这个问题。在这个场景中，由于我一次只接受单独的输入，所以我不需要添加`.join( “, ”)`方法，而是需要在添加元素之前添加所需的标点符号。此外，因为我想继续使用方法过程，所以我使用了`.pop()`来删除最后一个元素。

```
function unroll(matrix, endResult = “”) {
    //base case
    if (matrix.length === 0) {
      return endResult
    }

    //top
    endResult += (matrix.shift().join(“, ”))

    //right
    for (let i = 0; i < matrix.length - 1; i++) {
        endResult += “, ” + (matrix[i].pop())
    }

    //call itself
    return unroll(matrix, endResult + “, ”)
} 
```

底部行的脚本类似于顶部行，但是我需要使用`pop()`和`reverse()`来反转生成的数组，而不是`shift()`。

```
function unroll(matrix, endResult = “”) {
    //base case
    if (matrix.length === 0) {
      return endResult
    }

    //top
    endResult += (matrix.shift().join(“, ”))

    //right
    for (let i = 0; i < matrix.length - 1; i++) {
        endResult += “, ” + (matrix[i].pop())
    }

    //bottom
    endResult += “, ” + (matrix.pop().reverse().join(“, ”))

    //call itself
    return unroll(matrix, endResult + “, ”)
} 
```

为了将中间行的第一个元素添加到`endResult`字符串中，我需要使用一个`for-loop`。然而，这一次我需要做一个`reverse for-loop`倒计数，直到`i`的值等于 0。我还需要使用`shift()`来删除我正在迭代的数组的第一个元素。

```
function unroll(matrix, endResult = “”) {
    //base case
    if (matrix.length === 0) {
      return endResult
    }

    //top
    endResult += (matrix.shift().join(“, ”))

    //right
    for (let i = 0; i < matrix.length - 1; i++) {
        endResult += “, ” + (matrix[i].pop())
    }

    //bottom
    endResult += “, ” + (matrix.pop().reverse().join(“, ”))

    //left
    for (let i = matrix.length -1; i >= 0; i--) {
        endResult += “, ” + (matrix[i].shift())
    }

    //call itself
    return unroll(matrix, endResult + “, ”)
} 
```

我以为我完成了，但是我在我的脚本中发现了一个错误。得到的字符串有两个额外的字符`,`，需要删除。使用`.substring()`并将参数设置为`(0, endResult.length - 2)`隔离并删除最后两个字符，以返回所需的输出。
最终剧本是

```
function unroll(matrix, endResult = “”) {
    //base case
    if (matrix.length === 0) {
      return (endResult.substring(0, endResult.length - 2))
    }

    //top
    endResult += (matrix.shift().join(“, ”))

    //right
    for (let i = 0; i < matrix.length - 1; i++) {
        endResult += “, ” + (matrix[i].pop())
    }

    //bottom
    endResult += “, ” + (matrix.pop().reverse().join(“, ”))

    //left
    for (let i = matrix.length -1; i >= 0; i--) {
        endResult += “, ” + (matrix[i].shift())
    }

    //call itself
    return unroll(matrix, endResult + “, ”)
} 
```

瞧啊。矩阵展开了！希望你喜欢这个演练，并随时提供改进方法的输入！