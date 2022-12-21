# 分解 DSA:有效的字谜

> 原文：<https://dev.to/clairemuller/breaking-down-dsas-valid-anagram-1m55>

自从两个月前从熨斗学校毕业以来，我一直在努力学习数据结构和算法，为可怕的技术面试做准备。我在 LeetCode 上发现了一个很棒的[顶级面试问题集，并一直在慢慢地整理它们。](https://leetcode.com/explore/interview/card/top-interview-questions-easy/)

任何经历过这种编码挑战的人都知道，总是有多种运行时不同的方法和解决方案。我想，如果我谈谈我对最初遇到的一个问题的不同解决方案，以及我在这个过程中学到的东西，会对我(或许还有其他人)有所帮助。所以今天我要回答一个经典问题:有效的变位词。

问题是这样的:给定两个字符串，`s`和`t`，写一个函数来确定`t`是否是`s`的[变位词](https://www.dictionary.com/browse/anagram)。例如，如果`s = “anagram”`和`t = “nagaram”`，则返回`true`。我将使用 JavaScript。

我的第一个想法是，如果字符串的长度不一样，那么它们不可能是变位词，因此我们可以快速返回`false`。

```
if (s.length != t.length) {
  return false;
}; 
```

显然，两个字符串可以是相同的长度，但包含不同的字母，所以我们的函数继续。我的下一个(天真的)想法是将字符串转换成数组并排序。

```
s = s.split('').sort();
t = t.split('').sort(); 
```

然后，我可以通过遍历一个数组并对照另一个数组来比较每个字母。如果字母不相同，返回`false`。

```
for (let i = 0; i < s.length; i++) {
  if (s[i] != t[i]) {
    return false;
  }
} 
```

如果每个字母都通过了这个测试，那么这两个字符串就被确认为字谜，我们就可以返回 true 了！在提交了我的解决方案并感觉非常聪明之后，LeetCode 决定摧毁我的精神，告诉我我的解决方案的运行时只击败了其他 JavaScript 提交的 47%。

LeetCode 的伟大之处在于你可以看到其他人的解决方案，因此我掉进了兔子洞。我发现了一些人可能称之为“病态一号线”的东西，这与我的解决方案正在做的事情类似。

```
return s.split('').sort().join('') == t.split('').sort().join(''); 
```

从这个解决方案中，我了解到使用 for 循环比较两个数组的最后一步可以变得更容易，只需使用`.join()`将排序后的数组转换回字符串即可。这是因为字符串可以使用相等运算符进行比较，而数组则不能。

```
['wow'] == ['wow'];
// returns false;

'wow' == 'wow';
// returns true; 
```

进一步的研究让我学到了很多关于优化代码运行时的知识。我了解到，使用`.sort()`虽然快速且容易输入，但实际上效率很低。此外，在技术面试中，你可能无法使用一种对你来说很有用的方法。

然后，我学到了关于编写高效函数的非常重要的一课:对象为王。对象，或者其他编程语言中的字典，有一个固定的查找时间 O(1)，因此对优化代码非常有帮助。

所以我开始尝试用一个物体来解决这个问题。我决定可以遍历其中一个字符串，使用对象来记录每个字母出现的次数。因此，给定字符串“变位词”，对象将变成:

```
count = {a: 3, n: 1, g: 1, r: 1, m: 1} 
```

然后我可以遍历另一个字符串，首先检查这个字母是否出现在对象中，如果出现，从它的值中减去 1。看起来是这样的:

```
// create empty object
let count = {};

// iterate through one string, counting letters
for (let i = 0; i < s.length; i++) {
  count[s[i]] = (count[s[i]] || 0) + 1
};

// iterate through other string, checking the object has letter key
for (let i = 0; i < t.length; i++) {
  if (!count[t[i]]) {
    return false
  } else {
    // if letter is in object, subtract count
    count[t[i]] -= 1
  }
} 
```

我的解决方案有效，比其他 95%的 JavaScript 提交都快！但是我还想看看我是否能重构。我知道还有其他可能更干净的方式来编写我的 for 循环，所以我做了一些研究，发现了 [`for...of`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) 。我重构了我的两个 for 循环，结果是输入更少，代码更清晰！

```
// original for loop, iterating over the string, checking the object for each letter
for (let i = 0; i < s.length; i++) {
  count[s[i]] = (count[s[i]] || 0) + 1
};

// same loop using for...of syntax
for (letter of s) {
  count[letter] = (count[letter] || 0) + 1
} 
```

我应该注意到`for..of`和类似的`for...in`对 JavaScript 来说是新的，因此并不是所有地方都完全支持。但对我来说，这不是问题。所以我最后的函数看起来像这样:

```
var isAnagram = function(s, t) {
  if (s.length != t.length) {
    return false;
  };

  let count = {};

  for (letter of s) {
    count[letter] = (count[letter] || 0) + 1;
  }

  for (letter of t) {
    if (!count[letter]) {
      return false;
    } else {
      count[letter] -= 1;
    }
  }
  return true;
}; 
```

这就是所有的人！我度过了一段重构的美好时光，并在此过程中学到了很多东西。下周见！