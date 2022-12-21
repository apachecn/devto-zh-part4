# 细分每日生活津贴:两个总和

> 原文：<https://dev.to/clairemuller/breaking-down-dsas-two-sum-2gc>

又是一周，又是一篇博文！我真的很喜欢写我的上一篇关于解决一个流行的编码问题的文章，有效的字谜，我想我会在这周尝试另一篇。因此，今天我将使用 JavaScript 来介绍我对流行的两个和问题的各种解决方案。

这个问题有几个不同的变体，但是我要做的这个来自 [LeetCode](https://leetcode.com/problems/two-sum/) 。问题:给定一个整数数组，返回两个数的索引，这两个数相加得到一个给定的和。

```
Input: nums = [2, 7, 11, 15], sum = 9
Output: [0, 1]
Because nums[0] + nums[1] = 2 + 7 = 9 
```

我总是喜欢从最明显的强力解决方案开始，以确保我对问题有很好的理解。所以我的第一个解决方案是简单地使用两个嵌套的 for 循环来检查每个数字组合。如果两个数相加等于给定的和，则返回这两个数的指数。如果没有组合，返回一个空数组。

```
var twoSum = function(nums, sum) {
  for (let i = 0; i < nums.length; i++) {
    for (let j = i + 1; j < nums.length; j++) {
      if (nums[i] + nums[j] === sum) {
        return [i, j];
      }
    }
  };
  return [];
}; 
```

我很久以前就知道嵌套 for 循环的运行时间是 O( n^2)，这并不理想。总会有更好、更有效的方法，它通常涉及到对象/哈希表/字典/无论您选择的编程语言如何称呼它。

在对此思考了一分钟后，我意识到我可以遍历数组并将每个数字及其索引保存在一个对象中，给我这个:

```
// given nums = [2, 7, 11, 15]
obj = {2: 0, 7: 1, 11: 2, 15: 3} 
```

在构建这个对象时，我可以检查当前数字的补数(总和减去当前数字)是否已经存在于对象中。为了便于阅读，我将这个目标数字保存到一个变量中。

```
let target = sum - nums[i];

if (obj.hasOwnProperty(target)) {
  return [obj[target], i];
} 
```

这样，如果这两个数字在数组的开头附近，我们甚至不需要检查数组的其余部分，就可以返回。这个解决方案给了我们 O(n)的时间和空间，比使用嵌套 for 循环快一倍。最终的解决方案大概是这样的:

```
var twoSum = function(nums, sum) {
  let obj = {};

  for (let i = 0; i < nums.length; i++) {
    // save target number to variable, easier to read
    let target = sum - nums[i];

    // if the object has a key of the target number
    // return its index (the value) and current index of nums
    if (obj.hasOwnProperty(target)) {
      return [obj[target], i];
    }

    // otherwise, create key value pair of the current number and its index
    obj[nums[i]] = i;
  }
  return [];
}; 
```

谢谢收听，我们下周再见！