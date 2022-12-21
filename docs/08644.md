# 分解 DSA:计算素数

> 原文：<https://dev.to/clairemuller/breaking-down-dsas-count-primes-3d63>

欢迎回来！整整一个星期，我一直在试图想一个不是又一个算法故障的博客帖子想法，但昨天我遇到了一个疯狂酷的想法，我只想谈谈！这个问题来自我一直在研究的 [LeetCode 集合](https://leetcode.com/explore/interview/card/top-interview-questions-easy/)，它叫做[计数素数](https://leetcode.com/problems/count-primes/)。

问题来了:返回小于给定整数的素数个数，`n`。所以给定`10`，函数应该返回`4`，因为有 4 个质数小于 10: 2、3、5 和 7。

首先，我必须回忆一下质数到底是什么(不要评判我)。快速的谷歌搜索告诉我，质数是一个大于 1 的整数，它的唯一因子是 1 和它本身。我还学到了一个非质数叫做合数！令人着迷。

我的第一次尝试(用 JavaScript)非常简单。我创建了一个助手函数，`isPrime`，它只接受一个数字并返回一个布尔值。这个函数使用 for 循环遍历从 2 到给定数字的所有数字。我使用了[模/余数](https://www.w3schools.com/js/js_arithmetic.asp)操作符来检查循环中给定的数除以当前数的余数是否为 0。如果是，那意味着这个数不是质数，所以我们可以返回`false`。否则，该函数从`n > 1`返回一个布尔值，以清除 0 和 1。

```
function isPrime(n) {
  for (let i = 2; i < n; i++) {
    if (n % i === 0) {
      return false;
    }
  }
  return n > 1;
} 
```

现在我的`countPrimes`函数可以使用我的助手来计算素数。我在 0 处初始化了一个计数器，因为我们想要计数所有小于给定数字`n`的质数*，所以在开始 while 循环之前，我从`n`中减去 1。循环将`n`传递给助手，如果它返回`true`，我就迭代计数器。然后我递减`n`，再做一遍，最后返回最后的`primesCount`。这就是所有看起来的样子:* 

```
function countPrimes(n) {
  let primesCount = 0;
  n--;
  while (n > 1) {
    if (isPrime(n)) {
      primesCount++
    }
    n--;
  }
  return primesCount;
}; 
```

唷！我感觉很好，但我知道一定有更好的方法。检查每个数字是否都是质数是没有效率的，因为这样做需要将这个数字除以比它小的每个数字。那可是一大笔支票啊！在想不出更有效的解决方案后，我求助于我信赖的朋友谷歌。

这就是最酷的地方！我了解了厄拉多塞的[筛子，我大吃一惊。这个算法本质上是从第一个质数 2 开始，并将它的倍数标记为合数(不是质数)。然后，它移动到下一个素数，以此类推，直到达到给定的极限。](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)

我明白算法是如何工作的，但我仍然不确定用 JavaScript 实现它的最佳方式。更多的谷歌搜索让我看到了 Nic Raboy 的这篇很棒的文章。

想法是创建一个长度为给定整数`n`的布尔数组。最初，每个元素都会被标记为`true`，除了`0`和`1`，它们都不是质数。

```
let primes = [false, false];
for (let i = 2; i < n; i++) {
  primes[i] = true;
} 
```

现在，我们可以开始将质数倍数标记为假。我把这段代码归功于 Nic，因为这对我来说很难理解。他创建了一个`limit`变量，它是给定整数`n`的平方根。经过一番思考，我意识到这避免了检查数组中大于`n`的倍数。举个例子，如果`n = 10`我们只需要看小于它的平方根的素数，也就是 3.16。没必要看 5 和 7 的倍数。

```
let limit = Math.sqrt(n);
for (let i = 2; i < limit; i++) {
  if (primes[i] === true) {
    for (let j = i * i; j < n; j += i) {
      primes[j] = false;
    }
  }
} 
```

最后，我们的数组是完整的，我们可以简单地遍历它，计数每个`true`实例，返回最终计数！

```
let count = 0;
for (let i = 2; i < primes.length; i++) {
  if (primes[i] === true) {
    count++;
  }
}
return count; 
```

感谢跟随，我希望这是有帮助的人。我在学习的过程中获得了很多乐趣！再次，非常感谢尼克·拉博伊的帖子。下周见！