# 我在“Sum 3”中失败的所有方法

> 原文：<https://dev.to/rdelga80/all-the-ways-that-i-failed-at-sum-3-3a3c>

我的生活中有一个问题，它的名字叫 Leetcode。

当我试图在 DEV 上思考要写的主题时，我一直在阅读那些才华横溢的开发人员写的关于编码的权威文章，我不禁想，“我肯定做不到。”

似乎大多数编码人员对编码问题的解决方案和策略都非常清晰。与此同时，我解决问题的方式就像一只猴子拍打着按钮，直到一只香蕉弹出我的控制台，这样我就可以高兴地尖叫了。

但最重要的是享受这个过程。编码是如此有趣，所以我想分享我的编码失败(有时也有成功)。

## 总和 3

Sum 3 是对另一个名为 Sum 2 的 Leetcode 问题的卑鄙扭曲。在 Sum 2 中，循环遍历一个数组，将两个元素相加，直到它们等于目标数，而 Sum 3 使用三个元素。

哦，还有一个警告，每 3 个数组配对中每个元素只使用一次。

```
var sum3 = (array, target) => {
  // magic
}

var nums = [-1, 0, 1, 2, -1, -4]

sum3(nums, 0) // [ [-1, 0, 1], [-1, -1, 2] ] 
```

Enter fullscreen mode Exit fullscreen mode

### 尝试 1:用锤子敲打

对于这样的问题，我的第一个，也是完全无用的尝试，是把它推进绞肉机，转动曲柄，希望香肠最后出来。

在这种情况下，我尽可能多地运行 for 循环，祈祷其中有某种解决方案，然后疯狂地大笑，因为我的浏览器着火了，把我的硬盘融化成一滩黏糊糊的塑料。

尝试 1:

```
var nums = [-1, 0, 1, 2, -1, -4]

var threeSum = (nums, target = 0) => {
    var solution = []

    for(let a = 0; a < nums.length; a++) {
        for(let b = 0; b < nums.length; b++) {
            for(let c = 0; c < nums.length; c++) {
                let sum = nums[a] + nums[b] + nums[c]
                if (sum === target) solution.push([nums[a], nums[b], nums[c]])
            }
        }
    }
    return solution
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，直奔主题。没有混乱。不要大惊小怪。

让我们检查输出。

```
[
  [-1,-1,2],[-1,0,1],[-1,1,0],[-1,2,-1],[-1,2,-1],
  [-1,-1,2],[0,-1,1],[0,0,0],[0,1,-1],[0,1,-1],
  [0,-1,1],[1,-1,0],[1,0,-1],[1,0,-1],[1,-1,0],
  [2,-1,-1],[2,-1,-1],[2,2,-4],[2,-1,-1],[2,-1,-1],
  [2,-4,2],[-1,-1,2],[-1,0,1],[-1,1,0],[-1,2,-1],
  [-1,2,-1],[-1,-1,2],[-4,2,2]
] 
```

Enter fullscreen mode Exit fullscreen mode

哦天啊。是啊，这可不好。

哪里出了问题？在背面，我可以看到两个主要问题:

1.  我们在数组中反复使用同一个元素。
2.  到处都是复制品。

好了，我们将实现类似这样的东西来解决问题 1:

```
if (a !== b && b !== c && a !== c) {
  let sum = nums[a] + nums[b] + nums[c]
  if (sum === target) solution.push([nums[a], nums[b], nums[c]])   
} 
```

Enter fullscreen mode Exit fullscreen mode

我将把算法的核心放在一个条件中，如果任何变量在数组中的相同位置，那么求和数组就不会推入解数组。

```
[
  [-1,0,1],[-1,1,0],[-1,2,-1],[-1,-1,2],[0,-1,1],
  [0,1,-1],[0,1,-1],[0,-1,1],[1,-1,0],[1,0,-1],
  [1,0,-1],[1,-1,0],[2,-1,-1],[2,-1,-1],[-1,-1,2],
  [-1,0,1],[-1,1,0],[-1,2,-1]
] 
```

Enter fullscreen mode Exit fullscreen mode

复制问题有点棘手。

为了坚持这次尝试的主题，让我们扔一颗手榴弹，看看碎片如何准确地落在我们需要它们的地方。

首先，让我们对 sum 数组进行排序，这样我们就可以确保无论它在 nums 数组中的什么位置，它都处于相同的顺序。

```
if (a !== b && b !== c && a !== c) {
  let sum = nums[a] + nums[b] + nums[c]
  let sumArray = [nums[a], nums[b], nums[c]].sort((a, b) => a - b)
  if (sum === target) solution.push(sumArray)   
} 
```

Enter fullscreen mode Exit fullscreen mode

好极了。这给了我们什么？

```
[
  [-1,0,1],[-1,0,1],[-1,-1,2],[-1,-1,2],[-1,0,1],
  [-1,0,1],[-1,0,1],[-1,0,1],[-1,0,1],[-1,0,1],
  [-1,0,1],[-1,0,1],[-1,-1,2],[-1,-1,2],[-1,-1,2],
  [-1,0,1],[-1,0,1],[-1,-1,2]
] 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，JavaScript 不允许直接比较数组，所以类似于`[] === []`的东西不会起作用。

这意味着编写一个助手函数来检查新数组是否在解决方案数组中。

```
var isInSolution = (newArray, solutionArray) => {
    let dup = false
    let [a, b, c] = newArray

    solutionArray.forEach(array => {
        let [d, e, f] = array
        if (a === d && b === e && c === f) dup = true
    })

    return dup
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们必须调整内核，就像这样:

```
if (a !== b && b !== c && a !== c) {
  let sum = nums[a] + nums[b] + nums[c]
  let sumArray = [nums[a], nums[b], nums[c]].sort((a, b) => a - b)
  if (sum === target && !isInSolution(sumArray, solution))
    solution.push(sumArray)
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们点燃这个坏男孩。

```
[
  [-1,0,1],[-1,-1,2]
] 
```

Enter fullscreen mode Exit fullscreen mode

嘣！这就是如何完成 leet code 的方法。

以下是尝试 1 的完整代码:

```
var threeSum = (nums, target = 0) => {
    var solution = []

    for(let a = 0; a < nums.length; a++) {
        for(let b = 0; b < nums.length; b++) {
            for(let c = 0; c < nums.length; c++) {
                if (a !== b && b !== c && a !== c) {
                    let sum = nums[a] + nums[b] + nums[c]
                    let sumArray = [nums[a], nums[b], nums[c]].sort((a, b) => a - b)
                    if (sum === target && !isInSolution(sumArray, solution))
                        solution.push(sumArray)   
                }
            }
        }
    }
    return solution
}

var isInSolution = (newArray, solutionArray) => {
    let dup = false
    let [a, b, c] = newArray

    solutionArray.forEach(array => {
        let [d, e, f] = array
        if (a === d && b === e && c === f) dup = true
    })

    return dup
} 
```

Enter fullscreen mode Exit fullscreen mode

看看这些漂亮的代码。有函数调用函数，有一个线性排序函数的几个实例，箭头函数，逻辑测试...这是一些冗长的编码！

这就相当于开着一辆福特 F-150，轮胎是泥胎，镀铬轮圈，车身两侧涂着火焰，挂着一面大美国国旗，盖斯·布洛克斯在收音机里大放厥词，只是为了去街角的 711 便利店买半加仑牛奶。

这将是惊人的。

。

。

。

。

。

**超过时限**

良好的...我想把三个循环扔在一起不会有效地完成工作。

### 尝试 2:如果我只有一个大脑

既然锤子不起作用，那么是时候使用一点技巧，实际上想想这个问题在问什么了。

核心问题归结为这个: *a + b + c = 0* 。

如果你仔细想想，我们只需要知道其中两个数字，并确保第三个数字存在。

让我们从另一个角度来尝试这整件事:

```
var threeSum = (nums) => {
    var solution = []

    for(let i = 0; i < nums.length; i++) {
        var newArray = [...nums]
        var firstNum = newArray.splice(i, 1)[0]

        for(let j = 0; j < newArray.length; j++) {
            var secondNum = newArray[j]
            let diff = (firstNum + secondNum) * -1
            if (newArray.indexOf(diff) > -1 && newArray.indexOf(diff) !== j)
                solution.push([firstNum, secondNum, diff])
        }
    }

    return solution
} 
```

Enter fullscreen mode Exit fullscreen mode

这次尝试最大的不同是:

```
let diff = (firstNum + secondNum) * -1 
```

Enter fullscreen mode Exit fullscreen mode

这应该可以加快速度，让我们在之前的尝试中去掉一个 for 循环(*剧透:它没有*)。

当我们运行第一次测试运行时，我们得到这个结果:

```
[
  [-1,0,1],[-1,1,0],[-1,2,-1],[-1,-1,2],[0,-1,1],
  [0,1,-1],[0,-1,1],[1,-1,0],[1,0,-1],[1,-1,0],
  [2,-1,-1],[-1,-1,2],[-1,0,1],[-1,1,0],[-1,2,-1]
] 
```

Enter fullscreen mode Exit fullscreen mode

这一切开始看起来有点太熟悉了。

所以我们按照之前的步骤，检查重复，等等等等。

**超过时限**

好吧，如果我到目前为止学到了什么，如果你需要一个非常慢的算法，那么我是你的人(如果感兴趣的话，DM)。

### 尝试三:耍小聪明

我不知道这些问题是不是专门为了让你从众所周知的过度思考的迷宫中走出来，或者是我小时候太喜欢用我的 He-Man 动作玩偶推倒我的乐高积木。

a + b + c = 0 的思维方式肯定有问题。避免过多地遍历循环的想法也是有道理的。

在思考问题的时候，我的脑海里突然出现了一些东西；旧的可靠的**合并排序**算法。合并排序的关键是不断将数据分割成越来越容易管理的部分，直到像比较两个值一样简单。

让我们试试这个...

就像思路加 *a + b + c = 0* 一样，如果一个数组是有序的，那么这两个极端应该可以设置什么样的参数有可能等于 0。

取我们的样本数组:`[-1, 0, 1, 2, -1, -4]`

当组织起来时，它变成:`[2, 1, 0, -1, -1, -4]`，这意味着两个最极端数字的和是:`2 + (-4) = -2`。

越靠近中心，数字越接近零。

由于极值当前加到了`-2`上，那么将最后一个元素移近中心将增加总数，反之亦然，如果总和为正。

这样做的最大好处是我们将两个循环减少了一半。

所以让我们开始吧。

```
var threeSum = nums => {
    const solutionArray = []

    nums = nums.sort((a, b) => b - a) 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们将按降序(从最高到最低)对 nums 数组进行排序。

接下来，我们将添加几个故障保险-你知道，只是以防 Leetcode 试图像他们一样狡猾地欺骗我们。

```
var threeSum = nums => {
    const solutionArray = []

    nums = nums.sort((a, b) => b - a)

    if (nums.length < 3) {
        return solutionArray
    }

    if (nums.length < 4) {
        if(nums[0] + nums[1] + nums[2] === 0) return [nums]
        return []
    } 
```

Enter fullscreen mode Exit fullscreen mode

然后我们进入算法的核心。

首先我们要遍历数组。

```
 nums.forEach((num, i) => {
        let j = i + 1
        let k = nums.length - 1 
```

Enter fullscreen mode Exit fullscreen mode

神奇之处在于我们分配的两个变量:

*   这将是我们第一个半循环的开始
*   这是我们第二个半循环的结尾

“J”是数组中当前 forEach 元素之后的下一个元素*，而“k”是数组中的最后一个元素。*

我们将一起遍历数组的两端，看看有多少加到我们的目标数 0，直到 j 和 k 相遇(又名`j < k`)。

```
while (j < k) {
  let sum = num + nums[j] + nums[k]
  if (sum === 0) {
    solutionArray.push([num, nums[j], nums[k]].sort((a, b) => a - b))
  }

  sum > 0 ? j++ : k--
} 
```

Enter fullscreen mode Exit fullscreen mode

当 I 迭代时，j 和 k 元素的移动量在每次迭代中变得更短。

它的简单令人讨厌。当然，在我第一次看到这个问题的时候，我是不会意识到的。

这是完整的代码，包括一些其他特殊情况下的调整:

```
var threeSum = nums => {
    const solutionArray = []

    nums = nums.sort((a, b) => b - a)

    if (nums.length < 3) {
        return solutionArray
    }

    if (nums.length < 4) {
        if(nums[0] + nums[1] + nums[2] === 0) return [nums]
        return []
    }

    nums.forEach((num, i) => {
        let j = i + 1
        let k = nums.length - 1

        if (i !== j && num !== nums[i - 1]) {
            while (j < k) {
                let sum = num + nums[j] + nums[k]
                if (sum === 0) {
                    solutionArray.push([num, nums[j], nums[k]].sort((a, b) => a - b))
                    // this step is to skip duplicate numbers in the array
                    while(nums[j] === nums[j + 1]) j++
                    while(nums[k] === nums[k - 1]) k--
                }

                sum > 0 ? j++ : k--
            }
        }
    })
    return solutionArray
} 
```

Enter fullscreen mode Exit fullscreen mode

点击提交和**成功**

这绝对不是我能想出来的事情。但是，从不同的方向探索这个问题会导致某些见解，从而为解决方案让路。

如果有的话，那就是我想要传达的。我不知道其他程序员是如何做到的，但我肯定错误的 ***方式*** 比我正确的多。

但这完全没关系，因为每次失败总会让你有所领悟，你永远不知道什么会导致解决问题的突破。

迎接下一个挑战，迎接下一次失败！