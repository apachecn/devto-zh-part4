# 3 或 5 的倍数之和-问题= >解决方案

> 原文：<https://dev.to/nomangul/sum-of-3-or-5-multiples-problem-solution-292h>

# 问题

求 1000 以下所有 3 或 5 的倍数之和。例如，如果我们列出所有小于 10 的自然数，它们都是 3 或 5 的倍数，我们得到 3、5、6 和 9。这些倍数之和是 23。

[![code problem](img/5f5f8d2b46b31147dc151181a092fd01.png)](https://i.giphy.com/media/xT8qBsOjMOcdeGJIU8/giphy.gif)

* * *

# 方案

我们实现了两个解决方案，但首先我想提到两个解决方案中的共同功能...

```
const isMultiple = (num, mode) => (num % mode ? false : true);

// Checks the given number is multiple or not with the help of modulus

// e.g. isMultiple(6, 3) returns true because 6 % 3 remainder is 0.
// This mean 6 is multiple of 3.

// At the other hand isMultiple(26, 5) returns false 
```

* * *

# 用= >的阵法

1.

```
// initial array range/length

const numRange = 1000;

// Empty Array with 1000 undefined items

const array1000 = [...Array(numRange)]; 
```

2.

```
// array where we push 3 or 5 multiples

const threeOrFiveMultiples = [];

// common util function for checking multiples

const isMultiple = (num, mode) => (num % mode ? false : true);

// mapping over an undefined items array and pushing
// 3 or 5 multiples indexes to threeOrFiveMultiples array

array1000.map((_, index) => {
  if (isMultiple(index, 3) || isMultiple(index, 5))
    threeOrFiveMultiples.push(index);
}); 
```

3.

```
// reducing the array to a single value

const sum = threeOrFiveMultiples.reduce((acc, curr) => {
  return acc + curr;
}, 0);

// logs the sum of all the multiples of
// 3 or 5 below 1000 => 233168

console.log(sum); 
```

4.

```
// complete solution with array methods

// initial array range/length
const numRange = 1000;

// Empty Array with 1000 undefined items
const array1000 = [...Array(numRange)];

// array where we push 3 or 5 multiples
const threeOrFiveMultiples = [];

// common util function for checking multiples
const isMultiple = (num, mode) => (num % mode ? false : true);

// mapping over an undefined items array and pushing
// 3 or 5 multiples indexes to "threeOrFiveMultiples" array
array1000.map((_, index) => {
  if (isMultiple(index, 3) || isMultiple(index, 5))
    threeOrFiveMultiples.push(index);
});

// reducing the array to a single value
const sum = threeOrFiveMultiples.reduce((acc, curr) => {
  return acc + curr;
}, 0);

// logs the sum of all the multiples of
// 3 or 5 below 1000 => 233168
console.log(sum); 
```

* * *

# 以= >为循环

1.

```
let sum = 0;

// loop max range

const numRange = 1000;

// common util function for checking multiples

const isMultiple = (num, mode) => (num % mode ? false : true); 
```

2.

```
// looping over an numbers below 1000 and adding 3 or 5
// multiples to sum

for (let i = 0; i < numRange; i++) {
  if (isMultiple(i, 3) || isMultiple(i, 5)) sum += i;
}

// logs the sum of all the multiples of
// 3 or 5 below 1000 => 233168

console.log(sum); 
```

3.

```
// complete solution with For Loop

let sum = 0;

// loop max range
const numRange = 1000;

// common util function for checking multiples
const isMultiple = (num, mode) => (num % mode ? false : true);

// looping over an numbers below 1000 and adding 3 or 5
// multiples to sum
for (let i = 0; i < numRange; i++) {
  if (isMultiple(i, 3) || isMultiple(i, 5)) sum += i;
}

// logs the sum of all the multiples of
// 3 or 5 below 1000 => 233168
console.log(sum); 
```

[![problem solved](img/ef531fb686f3c3b6c9d6e65eec27b6a0.png)](https://i.giphy.com/media/JozPUJqrzDjZRXCTI5/giphy.gif)

* * *

让我知道你更喜欢哪个解决方案，为什么？(顺便说一句，我最喜欢的是“For 循环”,因为它很简单，代码也很少)

*更多有趣的内容，请在 Twitter 上关注我 [@NomanGulKhan](https://twitter.com/NomanGulKhan) 和 GitHub[@ NomanGul](https://github.com/NomanGul)T5】*

* * *

<center>**More from Noman 🦄**</center>

[![nomangul image](img/e8d1b3da89f774b1d4b2e13ee9179b73.png)](/nomangul) [## 🧔全栈开发用 2 分钟解释

### 诺曼古尔 9 月 13 日 191 分钟阅读

#explainlikeimfive #javascript #react](/nomangul/full-stack-dev-explained-in-2-minutes-1amn)[![nomangul image](img/e8d1b3da89f774b1d4b2e13ee9179b73.png)](/nomangul) [## 右边距:-50%；

### 诺曼古尔 9 月 11 日 191 分钟阅读

#jokes #css #watercooler](/nomangul/margin-right-50-4oko)