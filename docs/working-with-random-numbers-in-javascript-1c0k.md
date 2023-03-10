# 在 JavaScript 中使用随机数

> 原文：<https://dev.to/shimphillip/working-with-random-numbers-in-javascript-1c0k>

不可预测的行为使我们的应用程序在正确操作时更有趣。例如，想象一下在纸牌游戏中，你每一轮都要用同一套牌，而不是在每局游戏结束时洗牌来接收新的牌！我们肯定需要某种随机化来使我们的生活更有趣:)

## Math.random()

在 JavaScript 中，我们有一个名为 Math.random()的内置方法。我们不会详细讨论 Math.random()是如何实现的，但是让我们讨论一下如何利用 Math.random()来生成我们想要的随机数。让我们首先在控制台中运行 Math.random()。

```
Math.random() // 0.34484257625111736 
```

Math.random()返回一个介于 0(含)和 1(不含)之间的浮点数(带小数的数字)。知道了这个行为，我们就可以设置它，让它伸缩到我们想要的范围！假设我们想要一个 1 到 10 之间的随机整数(没有小数的数字)。下面是我们的做法。

```
Math.floor(Math.random() * 10) + 1 // 4 
```

我们将 Math.random()的输出乘以 10，这将始终返回一个介于 0 和 9.9999999 之间的浮点数...因为 0 乘以 10 还是 0 和 0.9999999...10 的倍数是 9.9999999...(Math.random()永远不会达到 1)。

其次，我们使用 Math.floor()将其向下舍入到最接近的整数。输出现在返回一个介于 0 和 9 之间的整数。

然后，我们将加 1 来抵消我们的输出！我们的公式现在生成一个 1 到 10 之间的数字。

## 更好的解决方案？

我们实际上可以通过使用 Math.ceil()而不是 Math.floor()来稍微缩短我们的代码。

```
Math.ceil(Math.random() * 10) // 8 
```

Math.ceil()接受一个数字并将*向上舍入到最接近的整数，这与 Math.floor()的作用相反。意味着最终不再需要+1。*

选择你喜欢的，尽管我更经常看到第一种方法。

## 复用为赢！

如果你读过我的文章。你知道我有多关注代码的可重用性。使用上面我们学到的技术，让我们创建一个多功能函数，它接受一个最小数和一个最大数，并输出一个介于两个参数之间的数。实际上，在我给你编代码之前。你为什么不试一试作为练习呢？

[https://repl.it/@shimphillip/random-number?lite=true](https://repl.it/@shimphillip/random-number?lite=true)

## 步骤

你拿到了吗？如果你没有，不要担心。第一次真的很难理解。

我们知道 Math.random()给出一个从 0 到 0.9999999 的浮点数...我们还知道 Math.floor()将数字向下舍入到最接近的整数。因此，`Math.floor(Math.random())`的结果总是 0。那么，我们怎么做才能得到一个以上的整数呢？Math.floor()中的可能数字必须大于 1！

回想一下，Math.random()乘以 10 给出了 10 种可能性。如果我们乘以 5 或 2 呢？

```
Math.floor(Math.random() * 10) // 0 - 9 (10 possibilities)
Math.floor(Math.random() * 5) // 0 - 4 (5 possibilities)
Math.floor(Math.random() * 2) // 0 - 1 (2 possibilities) 
```

好的，让我们给出一个场景，说我们想要一个 10 到 20 范围内的随机整数。让我们把 10 作为最小值，20 作为最大值。这意味着我们需要用最大值和最小值之差乘以 Math.random()。这是我们目前掌握的情况。

```
function getRandomNumber(min, max) {
  return Math.floor(Math.random() * (max - min))
} 
```

这实际上转换成了我们场景开始时的样子。

```
Math.floor(Math.random() * (20 - 10)) // 0 - 9 (10 possibilities) 
```

然而，我们希望一些事情有所不同。请注意，我们现在正在寻找 *11 种可能性*而不是 10 种可能性，因为我们想要包括 10 种*和 20 种* (10，11，12，13，14，15，16，17，18，19，20)。所以让我们在公式中加 1。

```
function getRandomNumber(min, max) {
  return Math.floor(Math.random() * (max - min + 1))
} 
```

在我们的场景中是

```
Math.floor(Math.random() * (20 - 10 + 1)) // 0 - 10 (11 possibilities) 
```

最后，我们还关心我们的产品范围以及可能性的数量。我们需要什么才能从 0 - 10 提升到 10 - 20？在末尾添加 min。以下是解决方案。

## 解

```
function getRandomNumber(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
} 
```

我们的场景产生了

```
Math.floor(Math.random() * (20 - 10 + 1)) + 10 // 10 - 20 (11 possibilities) 
```

## 总结

Math.random()非常有用和强大，可以用于许多不同的目的。从从给定数组中随机选取一个值到掷骰子产生不同的数字，都是通过 Math.random()完成的。如果您还没有机会使用它，我希望您能在不久的将来的项目中使用它。感谢阅读！