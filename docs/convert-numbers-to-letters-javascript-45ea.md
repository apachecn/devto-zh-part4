# 数字到字母代码将数字转换成字母

> 原文：<https://dev.to/jaydray1/convert-numbers-to-letters-javascript-45ea>

### 将字母转换成数字，反之亦然。

## tl，dr:

[ASCII 表](http://sticksandstones.kstrom.com/appen.html)

#### 数字到字母:

`let findNum = 22;`
`let num = String.fromCharCode(97 + findNum)`
`console.log(num)`

#### 字母到数字:

`let uni = 'y'.charCodeAt(0)`
`console.log(uni)`
T2】

解决方案的基础:ASCII 和字符串原型及其方法。
**ASCII 代表什么？**
*美国信息交换标准代码:*基本上就是我们在电脑上使用的，映射到一个数字上的符号。例如:
`ASCII CODE 97 : a`
`ASCII CODE 98 : b`
`ASCII CODE 99 : c`
`ASCII CODE 110 : n`
*等等等等...*
**在 JavaScript 中我们如何使用？**
字符串原型和原型上的 fromCharCode()方法。
`fromCharCode()`即“来自字符码”，带有`String` =“将我提供给你的字符码中的这个数字转换成一个字母。”
因为我们在 ASCII 表中的字母从 97 开始我们的 charCode = 97+" Number we provided "对于我们的情况，在 97-122 之间，a-z .
`let findNum = 22;`
`let num = String.fromCharCode(97 + findNum)`
`console.log(num)`
`> w`
**将字母转换为数字**
*side 注意:ASCII 是 Unicode 标准的一部分。Unicode 是一个由数千个符号地图组成的庞大列表，ASCII 包含在 Unicode 系统中(一年级解释；提前为冒犯真正聪明的一年级学生道歉)。*
所以如果我们有字母‘y’。
`let uni = 'y'.charCodeAt(0)` 0 是我们想要的字符串的字符的索引。
`console.log(uni)`
`>121`
就这样——感谢阅读！非常非常欢迎评论，这里有很多东西可以学习！