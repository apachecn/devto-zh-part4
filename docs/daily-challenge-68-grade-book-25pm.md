# 每日挑战#68 年级的书

> 原文：<https://dev.to/thepracticaldev/daily-challenge-68-grade-book-25pm>

今天的挑战是编写一个接受三个整数值的函数，计算平均值，然后返回与该成绩相关的字母值。如果这三个整数的平均值以一个大于 5 的数字结尾，在字母等级后面加上一个加号。如果小于 5，则附加一个减号。

```
Numerical Score      Letter Grade
90 <= score <= 100    'A'
80 <= score < 90      'B'
70 <= score < 80      'C'
60 <= score < 70      'D'
 0 <= score < 60      'F'

```

例子:
`grade(64, 55, 92)`=>`C-`(70.3)
`grade(99, 89, 93)`=>`A-`(93.6)
`grade(33, 99, 95)`=>`C+`(75.6)

编码快乐！

* * *

这次[挑战](https://www.codewars.com/kata/55cbd4ba903825f7970000f5)的灵感来自 CodeWars 用户 danleavitt0。感谢 [CodeWars](https://codewars.com/) ，他已经在[双条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！

想为未来的帖子提出一个挑战性的想法吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！