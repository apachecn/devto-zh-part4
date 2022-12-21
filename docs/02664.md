# 更新生成

> 原文：<https://dev.to/pykashchain/updationofidgeneration-53ea>

用户 Id 生成:Joseph 的团队被分配了一项任务，为在线游戏竞赛的所有参与者创建用户 Id。Joseph 已经设计了使用参与者的名、姓、PIN 码和号码 n 来生成用户 id 的过程。Joseph 定义的过程如下-
步骤 1:-
比较参与者的名和姓的长度。
短的称为“小名”，长的称为“长名”。如果名和姓的长度相等，那么按字母顺序排在后面的名字将被称为“长名”。
第二步:
用户 id 应生成如下-
较小姓名的最后一个字母+较长姓名的整个单词+从左至右遍历 PIN 时 PIN 位置 N 处的数字+从右至左遍历 PIN 时 PIN 位置 N 处的数字。
步骤 3:
切换步骤 2 中生成的 user-id 的字母，即大写字母应变成小写字母，小写字母应变成大写字母。让我们看几个例子；
例 1 :=如果参与者明细如下
First_name = Rajiv
Last _ Name = Roy
PIN = 560037
step 1:Last _ Name 的长度小于 First _ Name 的长度， 所以较小的名字是“Roy”，较长的名字是“Rajiv”
步骤 user _ id 将=“较小名字的最后一个字母+较长名字中的整个单词+从左到右遍历 PIN 时 PIN 中位置 N 处的数字+从右到左遍历 PIN 时 PIN 中位置 N 处的数字=最后一个字母“Roy”+Rajiv 中的整个单词+PIN 中从左到右的第 6 个数字 iof PIN 中的第 6 个数字。
步骤 3:
切换步骤 2 中生成的 user-id 的字母，即大写字母应变成小写字母，小写字母应变成大写字母。

[https://repl.it/@VikasTomar/Id-Generation?lite=true](https://repl.it/@VikasTomar/Id-Generation?lite=true)