# 每日挑战#69 -去电影院

> 原文：<https://dev.to/thepracticaldev/daily-challenge-69-going-to-the-cinema-3mng>

今天的挑战是帮助约翰做出一个重要的财务决定！

约翰喜欢去看电影，但他想找到最划算的方式。他可以花 15 美元买一张票，也可以花 500 美元买一张会员卡。他每用一次会员卡，门票就会是他上一次所付价格的 0.9 倍。

《出埃及记》如果约翰去电影院三次:
`A : 15 * 3 = 45`
`B : 500 + 15 * 0.90 + (15 * 0.90) * 0.90 + (15 * 0.90 * 0.90) * 0.90 ( = 536.5849999999999, no rounding for each ticket)`

创建一个带有三个参数的函数`movie`:`card`(卡的价格)`ticket`(一张票的正常价格)`perc`(他为前一张票支付的分数)并返回第一个`n`，这样`ceil(price of B) < price of A`

更多例子:
`movie(500, 15, 0.9)`应还`43`
(带卡总价 634，带票 645)

`movie(100, 10, 0.95)`应该返回`24`
(带卡总价 235，带票 240)

祝你好运！

* * *

*今天的挑战来自 CodeWars 用户 g964。感谢 [CodeWars](https://codewars.com/) ，他在 [2 条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！*

想为未来的帖子提出一个挑战性的想法吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！