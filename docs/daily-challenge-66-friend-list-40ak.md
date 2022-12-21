# 每日挑战#66-好友列表

> 原文：<https://dev.to/thepracticaldev/daily-challenge-66-friend-list-40ak>

约翰邀请了一些朋友。他的清单是:

`s = "Fred:Corwill;Wilfred:Corwill;Barney:Tornbull;Betty:Tornbull;Bjon:Tornbull;Raphael:Corwill;Alfred:Corwill";`

你能做一个程序:
-使这个字符串大写
-按姓氏的字母顺序排序吗

当姓氏相同时，按名字排序。客人的姓和名出现在结果中，用逗号分隔在括号内。

因此函数`meeting(s)`的结果将是:

`"(CORWILL, ALFRED)(CORWILL, FRED)(CORWILL, RAPHAEL)(CORWILL, WILFRED)(TORNBULL, BARNEY)(TORNBULL, BETTY)(TORNBULL, BJON)"`

在两个不同的同名家庭中，两个人的名字也可能相同。

* * *

*今天的挑战来自 [CodeWars](https://codewars.com/) 上的 g964，他已经在 [2 条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！*

想为未来的帖子提出一个挑战性的想法吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！