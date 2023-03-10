# 每日挑战#62 -约瑟夫斯幸存者

> 原文：<https://dev.to/thepracticaldev/daily-challenge-62-josephus-survivor-1olc>

约瑟夫斯和他的四十个人被罗马人困在一个山洞里。为了不泄露秘密或被抓，他们选择了集体自杀。他们围成一圈，每三个人就杀一个人。幸存的那个人本该结束自己的生命。

你们今天的挑战是写一个函数，返回约瑟夫斯排列中获胜的幸存者。它应该接受两个参数，总人数 *n* ，以及淘汰之间的步数 *k* 。

*比如用`n=7`和`k=3`，`josephus_survivor(7,3)`应该这样:*

```
josephus_survivor(7,3) => means 7 people in a circle;
one every 3 is eliminated until one remains
[1,2,3,4,5,6,7] - initial sequence
[1,2,4,5,6,7] => 3 is counted out
[1,2,4,5,7] => 6 is counted out
[1,4,5,7] => 2 is counted out
[1,4,5] => 7 is counted out
[1,4] => 5 is counted out
[4] => 1 counted out, 4 is the last element - the survivor!

```

祝你好运！快乐编码。

* * *

*今天的挑战来自于 [CodeWars](https://codewars.com/) 的 GiacomoSorbi，他已经在[双条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！*

想为未来的帖子提出一个挑战性的想法吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！