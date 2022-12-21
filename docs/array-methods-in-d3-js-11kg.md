# D3.js 中的数组方法

> 原文：<https://dev.to/aurelio/array-methods-in-d3-js-11kg>

数组是 JavaScript 中循环数据的自然数据结构。虽然 map、reduce、filter 等本机方法将为数组上的几乎所有操作提供良好的基础，但许多库和框架用特定于领域的实用程序丰富了本机函数集。

D3.js ，js 中事实上的数据可视化标准库，也不例外。D3 实际上提供了 [`d3-array`](https://github.com/d3/d3-array) ，一个完整的实用函数模块，涵盖了处理数据和可视化时许多最常见的操作。

让我们看看如何利用其中的一些，从最常用的开始。

## 正确安装和使用模块的方法

首先让我们指出，强烈建议只安装和导入`d3-array`模块，而不是整个 d3 模块。

这意味着，而不是这个:

```
// npm i -s d3
import d3 from 'd3';

d3.min([1,2,3]); 
```

你会想这样做:

```
// npm i -s d3-array
import { min } from 'd3-array';

d3.min([1,2,3]); 
```

这避免了下载和捆绑整个 d3 库。事实上，从 v3 开始，D3 已经变成了完全模块化的版本。这意味着我们可以只安装我们需要的部分，而不会因为包的大小而招致巨大的损失。

如果你想知道 d3 导出了哪些模块，你可以看到它们被很好地列在了 [index.js](https://github.com/d3/d3/blob/master/index.js#L2) 中。

* * *

为了说明 d3 的一些数组操作特性，让我们从两个虚拟数据集开始，一个稀疏的、无序的随机数数组，`data` :

```
const data = [1, 3, 4.2, 22, 1.33, undefined, 21, 4, 3]; 
```

..以及一个包含一些歌曲以及我对每首歌曲的评价的对象数组，`nestedData` :

```
 const nestedData = [{
  rating: 5,
  song: 'Song 2'
 }, {
  rating: 4,
  name: 'Top Man'
 }, {
  rating: 4.5,
  name: 'Clover Over Dover'
 }, {
  rating: 3,
  name: 'Pressure On Julian'
 }, {
  rating: 4,
  name: 'Yuko And Hiro'
 }
]; 
```

## `d3.min()`

用它来寻找数组中的最小值。
Js 提供了一个`Math.min()`操作符，但是`d3.min()`将被证明更加灵活和宽容，例如当我们的数据集是一个有洞的数组时(一些位置有空值或未定义的值，就像我们的数据数组一样)。在我们的例子中，结果如下:

```
const dataMin = d3.min(data); // => 1
const nestedMin = d3.min(nestedData, item => item.rating); // => 3 
```

我们来对比一下 Js，no-lib 版本的相同代码:

```
const dataMin = Math.min.apply(null, data); // => NaN :(
const nestedMin = Math.min.apply(
null,
nestedData.map(item => item.rating)); // => 3 
```

在第一种情况下，我们被稀疏数组所困扰，在第二种情况下，我们经历了一定程度的冗长。d3 版本当然更安全、更简洁、更紧凑。当然，我们可以迎合这种特殊情况，修复第一个方法:
返回的讨厌的 NaN

```
const dataMin = Math.min.apply(
null,
data.filter(item => typeof item !== 'undefined')); // => 1 
```

`d3.min`的实现可以在[这里找到](https://github.com/d3/d3-array/blob/master/src/min.js)。阅读下一个方法，了解它在 D3 中使用的一些常见注意事项。

## `d3.max()`

用它来寻找数组中的最大值。毫不奇怪，最大值与最小值正好相反。同样的道理也适用，所以在我们的两个数组中使用它就像这样简单:

```
const dataMax = d3.max(data); // => 22
const nestedMax = d3.max(nestedData, item => item.rating); // => 5 
```

`d3.max`的实现可以在这里找到[。同样，这个方法不计算`undefined`和`null`的值，它会安全地跳过它们。它会计算字符串，我们一会儿会看到。](https://github.com/d3/d3-array/blob/master/src/max.js)

只是为了好玩，我试着把所有东西都扔给‘D3 . max’和它的小对应物‘D3 . min’。一些(无意义？)测试包括:

```
d3.min(); // TypeError :(
d3.min(1); // undefined, make sure you pass an array!
d3.min(1, 2); // undefined, make sure you pass an array!
d3.min(null); // undefined
d3.min(['', 1, 2]); // => '' <= watch out for this one
d3.max(['', 1, 2]); // => 2
d3.min(['hey ho!', 1, 2]); // hey ho!
d3.max(['hey ho!', 1, 2]); // hey ho! 
```

这里最重要的一点是——除了建议创建合理的数据集(即不要复制上面的数据集)之外——在选择和比较空值时，要小心将它们存储为空字符串。不是说它经常发生，但无论如何知道它是好的。然而，比较字符串实际上会更有用。例如，我们可以在一组颜色中找到最暗的颜色。

```
d3.min(['#15234C', '#5a5f6d', '#183b9e', '#3d9e18']); // "#15234C" 
```

一个实际的例子是有一个调色板，并决定使用最深的颜色作为上限，最浅的颜色作为下限来绘制我们的数据。这将允许我们非常容易地为我们的数据可视化创建颜色范围。

这是一个非常强大的功能，如果你对它的实际使用感兴趣，一定要看看我关于在 d3.js 的范围内插值颜色的帖子。

## `d3.extent()`

用它在一次函数调用中找到最小值和最大值。返回一个数组。
在一个数组中寻找最小值和最大值是 D3 的一个常见操作，这样的功能被组合成一个方便的方法`extent`。
让我们试试吧。

```
d3.extent(data); // [1, 22]
d3.extent(nestedData, item => item.rating); // [3, 5] 
```

这与下面的代码完全相同，但更简洁:

```
const dataExt = [d3.min(data), d3.max(data)]; // [1, 22] 
```

我们只调用了一个方法，而不是两个。我以为在引擎盖下，`extent`只是一个包裹着`min`和`max`的包装。是[不是](https://github.com/d3/d3-array/blob/master/src/extent.js)。可以说，extent 最自然的用法是在计算数据集的范围和属性域时，在这种情况下，我们通常需要为计算比例提供最小和最大数量。

## `d3.sum()`

用它来获得一个数组中所有元素的和；

同样，正如所料，它跳过了空的和未定义的元素。

```
d3.sum(data); // 59.53
d3.sum(nestedData, item => item.rating); // 20.5 
```

这可以用普通的 Js 和 reduce 很容易地实现，但是不能检查空的或者未定义的元素会破坏这个聚会。

```
data.reduce((acc, item) => acc + item, 0); // => NaN :(
nestedData.reduce((acc, item) => acc + item.rating, 0); // 20.5 
```

## `d3.mean()`和`d3.median()`

用它们来寻找一个数组的平均值和中间值。

我们用两个非常相关的方法`mean`和`median`来结束今天的概述。
均值和中值的含义很容易混淆，所以我把这些方法放在一起，以便从解释它们的区别开始。这两个概念都代表了一个数据集的平均值，或者更确切地说，是一组统计分数的集中趋势。

**平均值**是平均值最广为人知和最常见的表达方式。它是通过将数据集中所有元素的总和除以它们的数量来计算的，或者-将它转化为 Js-t *数组元素的总和除以其长度*。

举个例子，让我们为此创建一个新的数组。

```
const arr = [1,2,3,4,5,8,10];
let sum = arr.reduce((acc, i) => acc + i, 0);
sum / arr.length // 4.714285714285714 
```

**中位数**是位于我们数据集中间的数字。我们的中值将是一半的项目位于它之前，另一半的值位于它之后的值。

没有简单的数学公式来计算中位数。因此，计算没有任何库的数据集的值将是一个更艰巨的挑战，因为当数据集长度为偶数和奇数时，我们必须考虑不同的行为。

给定这个排序后的数组:

```
const data1 = [1,2,3,4,5];
// ----------------^ 
```

我们的中值将是 3，因为这是位于中间的值，将数组“分成”两半。

不过在这种情况下:

```
const data2 = [1,2,3,4,5,6];
// -----------------^ uhm... 3 or 4? 
```

有**而不是**一个值分割数组，所以我们可以说 3 和 4 都位于中间。为了找到这种情况下的中值，我们必须将这两个值相加，然后除以 2(也就是说，我们将应用我们用来找到平均值的公式)。我们在这种情况下的中位数是 3.5。
幸运的是 D3 内置了这个功能，我们可以让这个库来帮我们计算。

因此，回到我们的原始数据集，我们可以很快找到平均值和中间值。

```
d3.mean(data); // 7.44125
d3.median(data); // 3.5
d3.mean(nestedData, i => i.rating); // 4.1
d3.median(nestedData, i => i.rating); // 4 
```

上面的结果突出了一个非常有趣的案例。虽然我们的歌曲集的均值和中值之间的差异可以忽略不计(4.1 和 4)，但当对我们的数字列表进行相同的计算时，我们不能说相同。这是为什么呢？

原因是平均值很容易受到数据集中峰值的影响。一个非常低或非常高的值很容易使我们的结果产生很大偏差。相反，中间值不会遵循同样的命运，这给了我们一个可以说更准确的概念，即中间点在哪里，以及中心趋势实际上是什么样子。

为了说明这一点，让我们在前面的数组中增加一个值，并借助 d3:
再次进行测试

```
const data3 = [1,2,3,4,5,1500000];
d3.mean(data3); // 250002.5
d3.median(data3); // 3.5 
```

现在很清楚这两个概念之间的细微差别实际上可能会转化为现实世界场景中的显著差异。这意味着，当我们需要表示一个数据集的平均值时，我们首先必须理解我们希望用户看到的“哪个平均值”,或者在通过可视化来讲述我们的故事时，哪个平均值更有意义。

这个问题没有唯一的答案，这是一个很好的理由，让我们清楚 D3 提供的这两个明显相似的方法的目的是什么。

* * *

这只是 d3 提供的作用于数组和映射的方法的一小部分，当然也是我使用最多的方法。正如你从[源代码](https://github.com/d3/d3-array/blob/master/src/index.js)中看到的，更多的被导出，并且很高兴知道其中可能有一个函数是为我们的需求定制的。

### 学分

封面图片由 [ShareGrid](https://unsplash.com/@sharegrid) 提供。

*原发表于我的[博客](https://nobitagit.github.io/blog/D3js-array-methods/)* 。