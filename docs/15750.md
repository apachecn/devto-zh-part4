# 现代 JavaScript 初学者指南-第 1 部分

> 原文：<https://dev.to/victorhazbun/modern-javascript-beginners-guide-part-1-4c10>

你开始编写 JavaScript 了..怎么知道自己写的 JS 是好是坏？

这是 JavaScript 入门的迷你指南。

好了，让我们从这本充满技巧和诀窍的酷指南开始吧。抓住一些

# 目录📦

*   [变量](#variables)
*   [发现神秘的楠](#spot-the-mystical-nan)
*   [如何对对象进行类型检查](#how-to-type-check-objects)
*   [功能默认参数](#function-default-parameters)
*   [其余参数](#rest-parameters)
*   [回调函数](#callback-functions)
*   [“这个”关键字](#the-this-keyword)
*   [速记属性](#shorthand-properties)
*   [析构对象属性](#destructuring-object-properties)
*   [值存在检查](#value-existence-check)
*   [财产存在检查](#property-existence-check)
*   [浅拷贝对象](#shallow-copy-objects)
*   [深层复制对象](#deep-copy-objects)
*   [析构数组](#destructuring-arrays)

## 变量

使用`let`定义可能改变的变量，使用`const`定义不应该改变的变量。现在你不应该使用`var`。

#### 明白让& const

使用`let`来表示在你的应用程序中的某一点可能会改变的变量。

使用`const`表示永远不会改变的变量。

[Javascript 变量](https://javascript.info/variables) ℹ️

#### 了解旧 var

你不应该再使用`var`。🙅‍♂️

[旧“var”](https://javascript.info/var)ℹ️

## 点出神秘的楠

> NaN 的意思是*不是数字*。🤯

这是检查一个数字是否为 NaN 的最好方法。
[https://stackblitz.com/edit/js-hpgwny?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-hpgwny?embed=1&view=editor&file=index.js)

*   第 **(1)** 行:试图将字符串`55px`解析为数字，但因为无法解析，所以返回`NaN`，所以`result`为`NaN`。
*   第 **(3)** :检查结果是否为`NaN`，为真。
*   第 **(4)** 行:检查`'this is false baby!'`是否为`NaN`，因为是`String`不是`NaN`，所以为假。
*   第 **(5)** 行:检查`NaN`是否是一个`NaN`，如果是，因为它是一个`NaN`。

## 如何对对象进行类型检查

这是检查对象类型的最佳方式。
[https://stackblitz.com/edit/js-cunxw2?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-cunxw2?embed=1&view=editor&file=index.js)

## 功能默认参数

这是为函数定义默认参数的最佳方式。
[https://stackblitz.com/edit/js-kuv4pu?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-kuv4pu?embed=1&view=editor&file=index.js)

*   Line **(1)** :通过使用`name =`，它允许你为你的函数参数定义一个默认参数。
*   第 **(5)** 行:覆盖默认参数。
*   第 **(6)** 行:使用默认参数。

## 休息参数

> 一个函数可以用任意数量的参数调用，不管它是如何定义的。

[静止参数和展开操作符](https://javascript.info/rest-parameters-spread-operator) ℹ️

还不理解？是的，我也在那里。🤷

基本上，这允许你发送尽可能多的参数，它们最终会被转换成一个数组。让我们来看看⚡
[https://stackblitz.com/edit/js-qwaswa?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-qwaswa?embed=1&view=editor&file=index.js)

*   Line **(1)** :通过使用`...prices`，它允许你传递任意数量的属性
*   第 **(7)** :调用以`10, 50, 35, 42, 28`为参数的函数。

但是假设您想要额外的参数...
[https://stackblitz.com/edit/js-9m7jic?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-9m7jic?embed=1&view=editor&file=index.js)

*   行 **(1)** :通过使用`...prices`，它允许传递任意数量的属性
*   第 **(6)** :调用以`'Jhon', 'jhon@example.com', 10, 50, 35, 42, 28`为参数的函数。

**注意:**额外的参数先去，其他的随后去(这些被称为“其余的”😜).

## 回调函数

好了，孩子们，这很重要。所以，你最好注意点👨‍🏫

> 作为参数传递给另一个函数的函数，通常作为事件发生的结果被调用。

[简介:回调](https://javascript.info/callbacks) ℹ️
[https://stackblitz.com/edit/js-p514ib?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-p514ib?embed=1&view=editor&file=index.js)
如你所见，我们已经定义了一个以名称和函数为参数的函数。

`sayHello`记录“Hello，Jhon ”,然后调用记录“Goodbye，Jhon”的函数`sayGoodbye`。注意 sayGoodbye 需要 name 参数。

## 【这个】关键词

好吧，这个很棘手。这是一个相当大的话题，所以...我将把你重定向到我的一个帖子:[最大的 JavaScript OOP gotcha](https://dev.to/victorhazbun/the-biggest-javascript-oop-gotcha-56ba) 😅

[对象方法，](https://javascript.info/object-methods) ℹ️《本》

## 速记属性

[https://stackblitz.com/edit/js-tbmy5l?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-tbmy5l?embed=1&view=editor&file=index.js)

*   第 **(7)** 行:使用变量`name`用`name`变量的值赋一个`property`“名称”。
*   第 **(8)** 行:使用变量`beer`将`gas`变量的值赋给一个叫做“气体”的`key`。

如果你不明白，喝点啤酒，过一会儿再来🍻

## 析构物体属性

希望你还是清醒的🥴

> 析构赋值是一种特殊的语法，它允许我们将数组或对象“解包”成一堆变量，因为有时这样更方便。析构也适用于具有大量参数、默认值等的复杂函数。

[析构赋值](https://javascript.info/destructuring-assignment) ℹ️
[https://stackblitz.com/edit/js-9nbgkv?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-9nbgkv?embed=1&view=editor&file=index.js)

*   Line **(10)** :定义一个对象，不要担心它会被用来定义我们的新变量，这意味着这个对象没有被赋值。
*   第 **(11)** :从`robot`中取属性`name`，定义一个变量`name`。
*   第 **(12)** 行:从`robot`中取`stealing`属性。`skills`并定义一个变量`stealing`。
*   第 **(13)** 行:获取剩余的`robot`属性，并将它们赋给一个变量`rest`。
*   第 **(14)** :使用`robot`定义变量。

## 值存在检查

这是检查给定对象上是否存在值的最佳方式。
[https://stackblitz.com/edit/js-l1m62a?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-l1m62a?embed=1&view=editor&file=index.js)

*   行 **(6)** :检查计算机对象上是否有一个值等于`127.0.0.1`的属性

## 财产存在检查

有几种方法可以检查对象属性的存在。
[https://stackblitz.com/edit/js-m6zuos?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-m6zuos?embed=1&view=editor&file=index.js)

*   第 **(5)** 行:当对象有太多关键点时，性能很差。
*   第 **(6)** 行:即使对象有太多关键点也能很好地执行。

## 浅抄物体

> 浅层副本将复制顶级属性，但嵌套对象在原始对象(源)和副本对象(目标)之间共享。

让我们来实验:
[https://stackblitz.com/edit/js-kemgrs?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-kemgrs?embed=1&view=editor&file=index.js)

*   行 **(9)** :对`robot`对象做一个浅拷贝。
*   线 **(19)** :将`robotClone`速度更新为`800`。

🐞WOOT🐞我们改变了两个机器人`speed`，`robotClone.skills`和`robot.skills`共享相同的对象引用，因为没有制作单独的副本，而是复制了对象的引用。对对象的任何属性所做的任何更改都会应用到使用该对象的所有引用。

**注意:**当你的对象有嵌套属性时不要浅拷贝(除非你真的想有这种行为)，而要用**深拷贝**。

## 深度复制对象

这是做深度复制的最好方法。
[https://stackblitz.com/edit/js-sqtpht?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-sqtpht?embed=1&view=editor&file=index.js)
通过使用**深层复制** VS **浅层复制**你可以注意到对原始对象没有副作用🎉

## 破坏数组

这与**析构对象**非常相似，但是因为数组没有键，而是有位置，所以我们需要访问我们想要返回的位置。【T2[https://stackblitz.com/edit/js-z7dz7u?embed=1&view=editor&file=index.js](https://stackblitz.com/edit/js-z7dz7u?embed=1&view=editor&file=index.js)

*   Line **(1)** :定义一个多维数组(你也可以使用一个常规数组，但是我想让你看看这个东西的威力💪).
*   第 **(3)** :我们定义一个变量`firstRobotName`，其值将为`robots[0]`，即`['Bender', 'Steal']`。然后定义一个变量`secondRobotState`(注意我们跳过了机器人的名字，而是取其状态`'Cool'`)。最后定义一个变量`rest`来取数组的其余部分。

## 不断学习🤓

*   [javascript.info](https://javascript.info)

## 最后的想法和下一步🚀

我们已经试验了一些现代概念，作为初学者你应该明白，这些技巧和窍门中的一些是今天完成我们过去用 JavaScript 做的事情的“最佳方式”。

写这篇文章的时候我很开心，我希望你能从中学到很多。现在，把它保存在你的书签里，这样你就可以快速参考日常的 JS 技巧。

我很想知道你还想学些什么，这样我就可以把它们包含在**现代 JavaScript 初学者指南**的**第 2 部分**中，里面会有很多非常酷的东西。👋