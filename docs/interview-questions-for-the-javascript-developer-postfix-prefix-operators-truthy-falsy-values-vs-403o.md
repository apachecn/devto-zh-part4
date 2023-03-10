# 面向 Javascript 开发人员的面试问题:后缀和前缀操作符、真值和假值、& ==与===

> 原文：<https://dev.to/bcaruthers/interview-questions-for-the-javascript-developer-postfix-prefix-operators-truthy-falsy-values-vs-403o>

在上一篇文章中，我们看了*提升*、*原型继承*，以及一个*属性*和一个*属性*之间的区别。你可以在这里找到那篇文章。今天，我们将继续通过查看*后缀*和*前缀递增/递减运算符*、*真值*和*假值*、*相等*与*恒等运算符*之间的差异来为面试做准备。

### 后缀&前缀递增/递减运算符

[![Mailboxes on a brick wall image](img/0e30de6a31e4961746117ad3ab86a27a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B43AArS0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dqejlzsb4/image/upload/v1560575775/kristina-tripkovic-602495-unsplash.jpg)

后缀(`x++` / `x--`)和前缀(`++x` / `--x`)递增/递减运算符用于将变量增加或减少 1，并返回该值。我们大多数人都熟悉 For 循环中的后缀增量运算符`i++`。所以如果他们都增加或减少一个变量，有什么区别呢？

如果它被用作后缀(`x++` / `x--`)，那么它在递增或递减变量之前返回值*。看看这个例子:*

[https://repl.it/@bcaruthers/Postfix-example?lite=true](https://repl.it/@bcaruthers/Postfix-example?lite=true)

如果使用前缀(`++x` / `--x`)，则在递增或递减变量后，返回值*。看看这个例子:*

[https://repl.it/@bcaruthers/Prefix-example?lite=true](https://repl.it/@bcaruthers/Prefix-example?lite=true)

### 真理&虚伪的价值观

[![Bowl of candy hearts image](img/cf795c2156d0b4046683f74b7ac9a28f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rf3WKYoR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dqejlzsb4/image/upload/v1560576098/obi-onyeador-559118-unsplash.jpg)

Truthy 和 falsy 是听起来很有趣的词。但是，它们是什么呢？JavaScript 在布尔上下文中对所有值使用类型强制。当一个值在布尔上下文中求值时，如果该值为真，则称为真，如果为假，则称为假。所有的值都被认为是真的，除了`false`、`0`、空字符串(单引号、双引号和模板文字)、`null`、`undefined`和`NaN`，它们都是假的。

### 等式与等式运算符

[![Equal stairs but diffrent colors image](img/100c61b27fc7b3b14865cc4a196d8490.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m10LG73f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dqejlzsb4/image/upload/v1560577676/greg-jeanneau-145138-unsplash.jpg)

那么`==`和`===`有什么区别呢？在任何必要的类型转换之后，*相等运算符* ( `==`)比较两个值是否相等。所以`1 == "1"`将返回`true`，就像`1 == 1`返回 true 一样。JavaScript 将字符串`"1"`转换成数字，然后将其与数字`1`进行比较。

*标识*或*严格相等运算符* ( `===`)比较两个值是否相等，但没有类型转换。所以这些值也必须是相同的类型。因此`1 === 1`将返回`true`，而`1 === "1"`将返回`false`。

我希望这篇文章能够帮助您更好地理解后缀和前缀操作符、truthy 和 falsy 以及等式和等式操作符。我要感谢所有读者对我第一篇关于 [Dev.to](//Dev.to) 的文章的大力支持。我还要感谢我在[的导师杰罗姆·哈达威、布拉德·汉基和菲尔·坦特罗马诺，感谢他们对我的培训、支持和督促我写文章。我祝你在采访中一切顺利，并在接下来的几周内继续关注本系列的更多文章。](https://vetswhocode.io/)

###### *照片演职员表(按出场顺序):*

###### [克里斯蒂娜·特里普科维奇](https://unsplash.com/@tinamosquito?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Kristina Tripkovic")

###### [奥比昂耶多尔](https://unsplash.com/@thenewmalcolm?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Obi Onyeador")

###### [【Greg jeanneau】](https://unsplash.com/@gregjeanneau?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Greg Jeanneau")