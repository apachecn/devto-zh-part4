# 每日挑战#51 -有效的花括号

> 原文：<https://dev.to/thepracticaldev/daily-challenge-51-valid-curly-braces-4e6o>

**Challenge**
编写一个函数`areCurlyBracesMatched`，它接受一个只包含`{`和/或`}`的字符串`s`，如果`s`匹配正确，则返回`true`，否则返回`false`。

如果字符串为空或者如果每个左括号都有相应的右括号，则认为字符串`s`匹配正确。

**例句**
`{{}}`搭配得当。
`{{{`搭配不当。
`{}{}{}`搭配得当。

`areCurlyBracesMatched("{{{}{}}}"), true;
areCurlyBracesMatched("{{"), false;
areCurlyBracesMatched("{}}"), false;
areCurlyBracesMatched(""), true;`

编码快乐！

* * *

这个来自 CodeWars 上的 only day。感谢 [CodeWars](https://codewars.com/) ，他已经在[双条款 BSD 许可](https://opensource.org/licenses/BSD-2-Clause)下许可了这个挑战的再分发！

想为未来的帖子提出一个挑战性的想法吗？发送电子邮件给**[yo+challenge @ dev . to](mailto:yo+challenge@dev.to)**提出您的建议！