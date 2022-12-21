# 欧拉项目解决问题，第一部分:3 和 5 的倍数

> 原文：<https://dev.to/dylanesque/problem-solving-with-project-euler-part-one-multiples-of-3-and-5-53bl>

这个帖子将包含第一个项目欧拉问题的潜在剧透/解决方案，所以如果你正在积极地工作，请谨慎阅读！

## 什么是欧拉项目？

根据 [freeCodeCamp](https://www.freecodecamp.org/) ，我第一次遇到这些问题的地方:

> 欧拉项目(发音为 Oiler)是一系列具有挑战性的数学/计算机编程问题，旨在探索不熟悉的领域，并以有趣和娱乐的方式学习新概念。
> 
> 这些问题的难度很大，对许多人来说，这种体验就是归纳链学习。也就是说，通过解决一个问题，它会让你接触到一个新的概念，让你着手解决一个以前无法解决的问题。
> 
> 虽然数学将帮助你找到优雅而有效的方法，但是使用计算机和编程技巧将是解决大多数问题所必需的。
> 
> *来自项目欧拉主页*

我将通过这些来详细说明如何解决问题，所以让我们进入第一个问题！

## 3 和 5 的倍数

关于 [FCC](https://learn.freecodecamp.org/coding-interview-prep/project-euler/problem-1-multiples-of-3-and-5) 的问题陈述:

> 如果我们列出所有 10 以下的自然数，它们都是 3 或 5 的倍数，我们得到 3、5、6 和 9。这些倍数之和是 23。
> 
> 找出所提供的参数值`number`以下所有 3 或 5 的倍数之和。

这里有一个明确的阅读理解测试，因为问题表明我们需要将所有低于作为参数传递给函数的数字相加，所以我会考虑这一点。

解决这个问题我需要哪些数据？我只需要一份上述数字的清单，然后把它们加起来。这是一个非常简单的问题，但是我将在以后的博客文章中详细介绍一个更加详尽的过程，这将有助于解决更加困难的问题。

首先，我以空数组的形式设置变量，将相关数字放入:

```
let numbers = []; 
```

Enter fullscreen mode Exit fullscreen mode

有几种方法可以用必要的数据填充数组，我现在想到的是:

*   建立一个`for`循环，将迭代器值设置为`number`减 1，当迭代器大于 2 时运行(最初设置为 0，但我在输入时意识到，3 下面显然没有 3 或 5 的正倍数，因此没有必要运行不必要的迭代)，并在每次遍历时从迭代器中减去 1。

*   循环将在每次循环中运行一个检查，看看迭代器值和(3 或 5)的[模数](https://en.wikipedia.org/wiki/Modulo_operation)是否等于零。如果是这样，该值将被[推送到](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)(读取:添加到数组的末尾)到`numbers`数组。这看起来像:

```
for (let i = number - 1; i > 2; i--) {
    if (i % 3 == 0 || i % 5 == 0) {
      numbers.push(i);
    } 
```

Enter fullscreen mode Exit fullscreen mode

最后，我要去跑[。减少`numbers`数组上的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)方法，并返回该值。`Array.reduce()`是需要学习的最复杂的内置 JavaScript 方法之一，但简短的版本是，它在一个数组上运行一个进程来*将它减少到一个值*。所以完整的代码看起来是这样的:

```
function multiplesOf3and5(number) {
  let numbers = [];

  for (let i = number - 1; i > 2; i--) {
    if (i % 3 == 0 || i % 5 == 0) {
      numbers.push(i);
    }
  }

  return numbers.reduce((a, b) => a + b, 0);
}

multiplesOf3and5(1000); 
```

Enter fullscreen mode Exit fullscreen mode

...而且很管用！

## 最后的想法

我可以在这里做更多的工作，包括分析算法的结果，并使用这些信息来改进运行时间。你也研究过这个问题吗？如果是，你的解决方案是什么样的？