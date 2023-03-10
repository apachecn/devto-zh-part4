# 每日挑战#67-电话目录

> 原文：<https://dev.to/thepracticaldev/daily-challenge-67-phone-directory-2d05>

约翰将他的旧个人电话簿备份为文本文件。在文件的每一行，他都能找到电话号码(格式为`+X-abc-def-ghij`，其中 X 代表一个或两个数字)`<`和`>`之间的对应姓名以及地址。

不幸的是，一切都是混杂的，事物并不总是按同样的顺序排列；部分行充斥着非字母数字字符(除了电话号码和姓名)。

约翰的电话簿线路示例:

`"/+1-541-754-3010 156 Alphand_St. <J Steeve>\n"`

`" 133, Green, Rd. <E Kustur> NY-56423 ;+1-541-914-3010!\n"`

`"<Anastasia> +48-421-674-8974 Via Quirinal Roma\n"`

你能帮助 John 编写一个程序吗？给定他的电话簿的行数和一个电话号码，这个程序返回这个号码的字符串:`"Phone => num, Name => name, Address => adress"`

示例:

`s = "/+1-541-754-3010 156 Alphand_St. <J Steeve>\n 133, Green, Rd. <E Kustur> NY-56423 ;+1-541-914-3010!\n"`

`phone(s, "1-541-754-3010") should return "Phone => 1-541-754-3010, Name => J Steeve, Address => 156 Alphand St."`

可以出现这样的情况，对于几个电话号码，有很多人对于一个电话号码——比如说 nb——然后返回:
`"Error => Too many people: nb"`

或者可能发生的情况是号码`nb`不在电话簿中，在这种情况下返回:
`"Error => Not found: nb"`

* * *

*今天的挑战来自 g964 [CodeWars](https://codewars.com/) ，他已经在 [2 条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这次挑战的再分发！*

想为未来的帖子提出一个挑战性的想法吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！