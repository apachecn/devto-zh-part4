# 每日挑战#74 -免费披萨

> 原文：<https://dev.to/thepracticaldev/daily-challenge-74-free-pizza-52lc>

为了提高销售额，你工作的比萨饼店的经理设计了一个新的比萨饼奖励系统。虽然，他不确定他想要什么样的变量。奖励制度将来可能会改变。您的经理希望您实现一个函数，在给定一个客户字典、最小订单数和最小订单值的情况下，返回一组有资格获得奖励的客户。

字典中的客户表示为:
`{ 'customerName' : [list_of_order_values_as_integers] }`

**测试 1:**
至少点 5 份订单，每份至少 20 美元，即可获得一份免费披萨！

```
min_orders = 5
min_price = 20
customers = {
'John Doe' : [22, 30, 11, 17, 15, 52, 27, 12], 
'Jane Doe' : [5, 17, 30, 33, 40, 22, 26, 10, 11, 45]
}

```

**测试 2:**
至少订购两次，每次至少 50 美元，即可获得一份免费披萨！

```
min_orders = 2
min_price = 50
customers = {
'Joey Bonzo' : [22, 67, 53, 29],
'Jennifer Bonzo' : [51, 19]
}

```

**测试 3:**
至少下 3 份订单，每份至少 15 美元，即可获得一份免费披萨！

```
min_orders = 3
min_price = 15
customers = {
'Natsumi Sakamoto' : [15, 15, 14],
'Gorou Hironaka' : [15, 15, 15],
'Shinju Tanabe' : [120, 240]
}

```

* * *

这个挑战来自《代码大战》中的眼镜王蛇。感谢 [CodeWars](https://codewars.com/) ，他在 [2 条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！

想为未来的帖子提出一个挑战性的想法吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！