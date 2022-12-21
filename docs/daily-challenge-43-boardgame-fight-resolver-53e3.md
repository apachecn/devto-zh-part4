# 每日挑战#43 -棋盘游戏战斗解析器

> 原文：<https://dev.to/thepracticaldev/daily-challenge-43-boardgame-fight-resolver-53e3>

今天的挑战是一个更大想法的一小部分。

您正在创建一个棋盘游戏，类似于火徽和象棋的混合。游戏有四个独特的角色:*剑客、骑兵、弓箭手和枪兵*。在与其他棋子的战斗中，每个棋子都有自己的优势和劣势。

您必须编写一个函数`fightResolve`，它将进攻和防守的棋子作为输入参数，并返回获胜的棋子。

两个棋子之间战斗的胜负取决于哪个棋子进攻，进攻棋子的类型和防守棋子的类型。

**弓箭手>剑士>枪兵>骑兵>弓箭手**

弓箭手总是赢剑士，剑士总是赢枪兵，枪兵总是赢骑兵，骑兵总是赢弓箭手。

如果发生了之前没有提到的比赛(例如弓箭手对枪兵)，攻击者总是会赢。

* * *

这个[挑战](https://codewars.com/kata/58558673b6b0e5a16b000028)来自 CodeWars 上的用户 Brysen。感谢 [CodeWars](https://codewars.com/) ，他已经在[双条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！

想要对未来的帖子提出挑战吗？发送电子邮件给**[yo+challenge @ dev . to](//yo+challenge@dev.to)**提出您的建议！