# 非常好而且相当准确的 JS 原始数据类型介绍(pt。1)

> 原文：<https://dev.to/ale3oula/the-quite-nice-and-fairly-accurate-introduction-to-javascript-primitive-data-types-e99>

JavaScript 有两种数据类型:**原语**，和**对象**。一个*原语*(或者一个原语值或者一个原语数据类型)，如 JavaScript 文档中所描述的，是不是一个对象并且没有方法的数据。

JavaScript 是一种*动态类型的*语言，这意味着即使在其生态系统中存在数据类型，变量也不会绑定到其中任何一种。

JS 中有 6 种原始数据类型:布尔、数字、字符串、空、未定义、符号(ES6)

**布尔型**

Boolean 是一种逻辑数据类型，它只能有两个值:true 或 false 这是一个是-否开关；逻辑运算产生一个布尔值；

**常见用例:**控制应用流程

[![boolean-1](img/e174df2455323e9aaa267a58afb5b19d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9jqKMqLj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/djpfBc7/boolean-1.png)

**号**

Number 是双精度 64 位浮点格式的数值数据类型。在 JS 中，number 既表示整数，也表示浮点。
一个数也可以是+无穷大、-无穷大、NaN(非数)。

**常见用例:**数学计算

[![number](img/f4f982c7840bb190e9b903ba431805e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TJol9u9---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/0r26jfC/number.png)

**字符串**

字符串是用来表示文本的字符序列。在 JS 中，字符串在双引号或单引号内。ES6 还引入了模板文字或模板字符串。模板文字是允许嵌入表达式(${})的字符串文字。对${…}中的表达式进行计算，结果成为字符串的一部分。

**常见用例:**存储文本

[![string](img/c989ee365d24174b9c8b96d91332ad0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--luLnX0F_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/Q6VnJvp/string.png)

**Null**

在编程中，null 通常表示指向内存中不存在/无效的对象或地址的引用。在 JS 中，虽然是一个特殊的原始类型，它表示“无”。

[![null](img/1b7c5cdca0300e26f7689fe148ead36f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z5WveEwW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/2vpVzpN/null.png)

*注意:【null 的类型是‘object’。它被认为是一个 bug ( [为什么 null 是对象](https://stackoverflow.com/questions/18808226/why-is-typeof-null-object)*

**未定义**

Undefined 是一种数据类型，它代表一个未定义的值；

**常见用例:**检查变量是否赋值

[![undef](img/a577f152585b9d762a740ac294e49d50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qggnXhrI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/Xp9z2pK/undef.png)

[Undefined vs Null](https://medium.com/@alyz26/undefined-vs-null-c567b539ee71)

**符号**

符号是在 ES6 中引入的，是完全唯一的标识符。就像其他原语一样，它们可以使用工厂函数 Symbol()创建，该函数返回一个符号(即 new Boolean('true ')创建一个新的布尔值)。

**常见用例:**创建密钥、隐私、协议

[![symbol-1](img/c507f77b6d7298e94bb569203661c9e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UTHBs-Yj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/DYHVMKG/symbol-1.png)

参考资料:

[JavaScript 符号的快速概述](https://www.freecodecamp.org/news/how-did-i-miss-javascript-symbols-c1f1c0e1874a/)

[JavaScript 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference)

[JavaScript 数据类型解释](https://codeburst.io/javascript-data-types-explained-347555cd2d4d)