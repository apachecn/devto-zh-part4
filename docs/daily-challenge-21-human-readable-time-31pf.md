# 每日挑战#21 -人类可读时间

> 原文：<https://dev.to/thepracticaldev/daily-challenge-21-human-readable-time-31pf>

今天的[挑战](https://www.codewars.com/kata/52742f58faf5485cae000b9a)来自 CodeWars 上的 davazp。

> 该函数将接受非负整数的输入。如果是零，它只返回`"now"`。否则，持续时间表示为`years`、`days`、`hours`、`minutes`和`seconds`的组合，按此顺序。
> 
> 结果表达式由类似于`4 seconds`、`1 year`等的组件组成。如果整数大于 1，时间单位用复数。组件由逗号和空格(“，”)分隔，除了最后一个组件由`" and "`分隔，就像它用英语书写一样。在这个挑战中，一年是 365 天，一天是 24 小时。注意空格很重要。
> 
> 这个挑战通过例子就容易理解多了:
> `format_duration(62) # returns "1 minute and 2 seconds"`
> `format_duration(3662) # returns "1 hour, 1 minute and 2 seconds"`

绝对是有用的代码。有时在编码时以秒为单位工作要容易得多，但在实际使用中读取人类时间更容易。

祝你好运！

* * *

*感谢 [CodeWars](https://codewars.com/) ，他已经在 [2 条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！*

想要对未来的帖子提出挑战吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！