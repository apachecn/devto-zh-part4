# 用记忆化优化你的 Javascript 程序。

> 原文：<https://dev.to/mongopark/optimize-the-hell-out-of-your-javascript-programs-with-memoization-11l8>

很多个月前，当我开始学习算法时，我刚刚学会了递归，感觉自己像个绝地武士。你知道他们说什么吗？:“如果你只有一把锤子，那么一切看起来都像钉子”。我试图用某种形式的递归来解决每一个可以想象的任务。事实证明这是个糟糕的主意。

当我试图用递归求解一长串斐波那契数列时，我猛然醒悟，我的计算机根本无法处理它。几个小时后，它仍然无法计算出结果。充分披露；从来没有，我放弃了，关闭了整个该死的事情，并开始重新考虑我曾经成为一名程序员的决定。为什么我不学学说唱，我可以成为下一个 Jay-Z。我不知道发生了什么。

[![Confused now.gif](img/1d609ca25edd070bc09d604c797b8145.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4wANSVZ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1563864716681/8a81fZvfH.gif)

那是我第一次思考最优化的概念。

如果你是好奇型的，运行未优化的递归斐波纳契数列，数列最大为 50.....明天见！😃

## 那么什么是优化呢？

那么什么是优化，为什么即使作为一个没有经验的开发者，你也需要开始思考它。

> 优化算法是通过比较各种解决方案反复执行的过程，直到找到最优或满意的解决方案。

例如，在设计的优化中，设计目标可以简单地是最小化生产成本或最大化生产效率。

那么，什么是**记忆**？

我知道你可能会认为我拼错了“记忆”。但是没有！我肯定我是指记忆。记忆化是计算机科学中的一个术语，指的是一种技术或优化模式，它通过存储复杂函数调用(在函数运行期间花费大量时间和消耗大量内存的函数)的结果，并在相同的输入或参数再次出现时返回存储在内存中的结果，来加速程序的执行。

呃！！够了，计算机科学术语！。我甚至没有计算机科学学位，你凭什么相信我的定义。请允许我给你看代码。

我将坚持使用差点让我放弃编程的斐波那契数列。我们将探索一个未优化的斐波那契函数的例子和另一个使用记忆优化的例子。

## 设置

能够想象出不同之处。我们需要一点一次性的设置。我是一个 Javascript 的家伙，我将使用一个节点环境。您可以使用您熟悉的任何性能指标。

一个 [NodeJS](https://codesandbox.io/s/admiring-sound-21d0i) 代码沙箱就足够了。让我们安装并要求`perf-hooks`。简单地从 perf-hooks 中要求`performance`如下:

```
const { performance } = require("perf_hooks"); 
```

现在让我们写一个递归计算第 n 个数的斐波那契数列的函数。

```
function fibonacci(n) {
  if (n === 0 || n === 1)
    return n;
  else
    return fibonacci(n - 1) + fibonacci(n - 2);
} 
```

该函数对于较小的“n”值表现良好。然而，随着“n”的增加，性能会迅速下降。这是因为这两个递归调用重复了相同的工作。比如计算第 50 个斐波那契数，递归函数必须调用 400 亿次以上(具体是 40730022147 次)！我们稍后会看到这个。

## 记忆化的斐波那契函数。

在 Fibonacci 函数的内存化版本中，当 f()被返回时，它的闭包允许它继续访问“memo”对象，该对象存储了它以前的所有结果。每次执行 f()时，它首先检查当前值“n”是否存在结果。如果是，则返回缓存的值。否则，执行原始的斐波那契代码。注意“memo”是在 f()之外定义的，这样它就可以在多次函数调用中保持其值。

```
var memoizeFibonacci = function() {
  var memo = {};

  function f(n) {
    var value;

    if (n in memo) {
      value = memo[n];
    } else {
      if (n === 0 || n === 1)
        value = n;
      else
        value = f(n - 1) + f(n - 2);

      memo[n] = value;
    }

    return value;
  }

  return f;
}; 
```

## 与`perf-hooks`对比表现。

让我们想象一下用这两个函数计算第 30 个斐波那契数所花费的时间。

```
//un-optimized
// time before function is executed
const startTime = performance.now();
fibonacci(20);
// time after function has completed computation
const endTime = performance.now();

console.log("Un-optimized time", endTime - startTime);

// memoized
const startTime2 = performance.now();
memoizeFibonacci(20);
// time after function has completed computation
const endTime2 = performance.now();

console.log("Optimized time", endTime2 - startTime2); 
```

```
//result

Un-optimized:  1020.0609370004386
Optimized:  0.049122998490929604 
```

您可以看到，我们已经将计算时间增加了 20000 倍以上。那只是 30 个数字的序列。这个例子很简单，可能看起来和你的日常任务不太一样，但是如果你仔细观察，你的程序中有一些东西是可以优化的。请记住，记忆化只是一种优化方法，还有无数不同的策略。不要做一个把每个问题都当成钉子的锤子。

还要注意的是，我们仅仅触及了记忆化的表面，这仅仅是为了打开我们的思维去面对各种可能性。

它有效并不意味着它不能被改进。前进并优化！

PS:标题有点夸张。它只是碰巧是我脑海中闪过的第 97 个标题😃