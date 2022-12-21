# 每日挑战#72 -矩阵转换

> 原文：<https://dev.to/thepracticaldev/daily-challenge-72-matrix-shift-2ab4>

写一个函数，将移动一个矩阵`n`次。

你将得到一个名为`m`的矩形矩阵，必须将矩阵末尾的`n`个字符移到开头，并将所有其他索引移过去。

**例子:**

```
  ['a','b','c','d']      ['h','a','b','c']
  ['1','2','3','4']  =>  ['d','1','2','3']
  ['c','o','d','e']      ['4','c','o','d']
  ['b','l','a','h']      ['e','b','l','a']

```

```
  ['d','u','d','e']      ['g','d','u','d']
  ['i','m','c','o']  =>  ['e','i','m','c']
  ['d','i','n','g']      ['c','o','d','i']

```

```
  ['h','i']      ['k','h']
  ['o','k']  =>  ['i','o']

```

祝你好运！

* * *

这个[挑战](https://www.codewars.com/kata/5afd3c451839f13b95000132)来自 CodeWars 的 Jomo Pipi。感谢 [CodeWars](https://codewars.com/) ，他已经在[双条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！

想为未来的帖子提出一个挑战性的想法吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！