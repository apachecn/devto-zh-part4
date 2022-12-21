# 每日挑战#55 -建造一堆立方体

> 原文：<https://dev.to/thepracticaldev/daily-challenge-55-building-a-pile-of-cubes-30on>

挑战
你的挑战是建造一座由一堆 *n 个*立方体组成的建筑。底部的立方体的体积将为 n^3 的体积，上面的立方体的体积将为 n-1)^3 的体积，以此类推，直到顶部的体积为 1^3.的体积

给你这个建筑的总体积。给定 m(T1 ),你能找到你需要建造的立方体的数量(T2)n(T3)吗？

函数 *f* 的参数将是一个整数 *m* 你要返回整数 *n* (比如 n^3 + (n-1)^3 +...+ 1^3 = m)或 0，如果没有这样的 n

**举例**
`f(1071225)`->-`45`-
-`f(91716553919377)`->-`0`

编码快乐！

* * *

*这个[挑战](https://www.codewars.com/kata/build-a-pile-of-cubes)来自于 CodeWars 上的 g964。感谢 [CodeWars](https://codewars.com/) ，他已经在[双条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！*

想为未来的帖子提出一个挑战性的想法吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！