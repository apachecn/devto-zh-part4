# 每日挑战#26 -排名位置

> 原文：<https://dev.to/thepracticaldev/daily-challenge-26-ranking-position-3c72>

我们将迎来又一周的挑战！

今天，让我们发挥一点创造力，开发一个排名系统，我们可以对分数进行排序，并计算个人或团队在游戏/比赛中的位置。

**注** :
两人或两人以上积分相同的，应具有相同的职位号，并按姓名排序(姓名唯一)。

例如，输入结构:

```
[
  {
    name: "John",
    points: 100,
  },
  {
    name: "Bob",
    points: 130,
  },
  {
    name: "Mary",
    points: 120,
  },
  {
    name: "Kate",
    points: 120,
  },
] 
```

输出应为:

```
[
  {
    name: "Bob",
    points: 130,
    position: 1,
  },
  {
    name: "Kate",
    points: 120,
    position: 2,
  },
  {
    name: "Mary",
    points: 120,
    position: 2,
  },
  {
    name: "John",
    points: 100,
    position: 4,
  },
] 
```

祝你好运！

* * *

*这个挑战来自用户 kzm。感谢 [CodeWars](https://codewars.com/) ，他在 [2 条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！*

想要对未来的帖子提出挑战吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！