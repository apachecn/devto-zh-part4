# 棘手的 Javascript 东西 01

> 原文：<https://dev.to/yongliang24/tricky-javascript-stuff-01-1065>

### 1。Javascript 对于空值有两种类型的值——null 和 undefined。这两个有什么区别？

未定义-

当一个变量的值没有被定义时，这样当我们创建一个变量而没有赋值时，这个变量被定义为“未定义的”。当我们用 typeOf 检查类型时，该类型也是未定义的。

一些未定义的方法:

1.  声明一个变量，但不给它赋值
2.  函数返回空值
3.  不向函数的参数传递任何值

Javascript 有一个名为 undefined 的全局变量，它的值和类型显示为 undefined。

空-

Null 表示没有值。它是一个原始类型，我们可以将 null 赋给变量。当我们检查 typeOf null 时，它作为 object 返回，但是，它不是真正的对象，因为我们不能向它添加属性。

### 2。== vs === ||是不是越多越好？

double ==比较运算符不检查类型，只检查值。对于 8 == "8 "，它将返回 true，即使 8 是一个数字，而" 8 "是一个字符串。

tripe ===操作符将检查类型和值。因此，8 === "8 "将返回 false，因为它们的类型不同。

NaN 是一个非数字类型，与 null 和 undefined 一样，这三个都是唯一的类型，不会等同于其他类型。

南甚至不等于它自己。NaN == Nan 或 NaN === NaN 都将返回 false。

来源:[https://www . thatjsdude . com/interview/js2 . html # nullVsUndefined](https://www.thatjsdude.com/interview/js2.html#nullVsUndefined)