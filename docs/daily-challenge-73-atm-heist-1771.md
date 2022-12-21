# 每日挑战#73 - ATM 抢劫

> 原文：<https://dev.to/thepracticaldev/daily-challenge-73-atm-heist-1771>

一个小偷想从一个非常特别的赌场的两台自动取款机里偷东西。

虽然他们主要关心的是如何带走钱，但他们也对这个特定赌场的自动取款机的一个独特事实感兴趣；每天一次，每台 ATM 机每隔一个单位的距离向另一台机器转账一美元(例如，在[0，1]中，两个指数相距 1 个单位)。此外，当提款机被清空时，每台提款机会自动重新注入与之前完全相同的金额，因此有可能从同一个提款机偷两次。

小偷最感兴趣的是从两个自动取款机中偷钱，这两个自动取款机里面的钱加起来最多，而且每一个自动取款机都向另一个自动取款机转账。您的函数应该返回这个值。

例如，如果我们有四台 ATM:[2，3，4，5]，索引为 0 的 ATM 将向索引 1 转账 1 美元，向索引 2 转账 2 美元，向索引 3 转账 3 美元。类似地，索引 2 处的 ATM 将 1 美元转到索引 1 和 3，2 美元转到索引 0。

请注意，在上面的案例中，我们的小偷要么从最后一台 ATM(索引 3)偷两次，要么从索引 0 和索引 3 偷，因为这使她净赚了最大值 10 美元(5 + $5 + $0 转账对$2 + $5 + $3 转账)。无论哪种方式，答案都是 10，以整数形式返回。

**例子:**

```
const atms = [3,1,3]
maximumThrill(atms) => 8 // $3 + $3 + $2 transferred between each (atms[0] and atms[2])

const atms = [2,3,4,5]
maximumThrill(atms) => 10 // $5 + $5 + $0 transferred (atms[3] and atms[3] again)

const atms = [10, 10, 11, 13, 7, 8, 9]
maximumThrill(atms) => 26 // $10 + $13 + $3 transfer between each (atms[0] and atms[3])

const atms = [2, 3, 4, 5, 10, 6, 7, 8, 9, 10, 11, 12, 4, 4, 2, 2, 12, 8]
maximumThrill(atms) => 34  // $10 + $12 + $12 transfer between each (atms[4] and atms[16])
```

注意:这些是神奇的自动取款机，所以不要担心计算自动取款机是否有足够的钱来转账。

祝你好运！

* * *

这个挑战来自于 CodeWars。感谢 [CodeWars](https://codewars.com/) ，他在 [2 条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！

想为未来的帖子提出一个挑战性的想法吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！