# 每日挑战#61 -进化率

> 原文：<https://dev.to/thepracticaldev/daily-challenge-61-evolution-rate-1k55>

写一个函数 getEvolutionRateMessage，它在前后取两个数。“之前”值是进化的初始值。后值是其进化后的值。一次进化可以计算如下:

`evolution = (after - before) / before * 100`

这个函数将返回一个字符串，这样就有三种可能的输出:
“X %的正演变”
“Y %的负进化。”
“没有进化”

示例:

getvolunterate message 11 . 29 45 . 79
getvolunterate message 95 . 12 . 66 . 84
getvolunterate message 0 . 27 . 35
getvolunterate message 41 . 26 0
getrevolutionrate message 1 . 26 1 . 26

“306%的积极进展。”
“负面进化 30%。”
“正面进化 2735%。”
“4126%的负面进化”
“没有进化。”

* * *

*这个挑战来自 DEV 的[阿明奈利](https://dev.to/aminnairi)。想为未来的帖子提出一个挑战性的想法吗？发送电子邮件**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！*