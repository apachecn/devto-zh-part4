# 并集和交集类型

> 原文：<https://dev.to/macmacky/union-and-intersection-types-2p82>

### 嗨伙计们日安！

**|** 操作符告诉我们可以让**或者**在右边的表达式中选择一个类型或者值。

[![Union](img/afeb7f9100ba39af20fcbf798a300919.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---YYQNbE2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h9m3keprekoewyedhqfe.png)

在这个例子中， **LogLevel** 类型意味着我们可以有一个值“**错误**”、“**致命**”、“**信息**”或“**调试**”。

[![Example Union](img/505236af6044eb2eacca0bd8c151bca0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oJsAlu7o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/elybuf02slr7z6xcn6wa.png)

在上面的例子中，我们创建了一个名为 **myLogger** 的函数，它有两个参数 **level** ，其类型为 **LogLevel** 和 **message** ，其值可以是**字符串**或**对象**。

我们调用 **myLogger** 函数，其值分别为**级别**和**消息**参数的**【err】**和**【Wtf some error occurs】**。如果您使用 **VS 代码**作为您的编辑器，将会看到一个编译时错误，并显示一条消息**“类型为“err”的参数不可分配给类型为“LogLevel”的参数。”**。显然这意味着 **"err"** 值与我们的类型 **LogLevel** 不兼容。

[![Right Code - Union](img/f8fe42a3ccb540f76fcfccfcad89d73c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wyfqQB39--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/btivd00c9dx4ecft2vsa.png)

但是如果我们将**“err”**改为**“error”**，编译时错误将会消失。

**&** 操作符告诉我们可以**组合**右边表达式上的值或类型。

[![Intersection](img/1e4880c90a12726fa6b38272e36cc5b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dkCufMcP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y5fklco0zddasn27nojk.png)

在上面的例子中，我们有两个接口 **APIResponse** 和 **APIData** 。
和一个叫**的函数回复**。 **resp** 参数具有 **APIResponse** 和 **APIData** 的**组合**类型，这意味着我们可以在 **resp** 参数中拥有两个接口的所有属性或成员。

[![Example Intersection](img/731559b02a50adff6418370d7a945612.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---JD1K4Qo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xdhdq1g2ph0tb2utislw.png)

在上面的例子中，我们制作了一个变量 **resp** ，它具有组合类型 **APIResponse** 和 **APIData** 。然后我们指定一个成员**wahhhh _ in _ _ resp**对象，它在两个接口中都不存在。将显示编译错误，并显示一条**类型的消息“{ response code:number；response message:string；订单:未定义[]；wahhhh:undefined[]；“}”不可赋给类型“APIResponse & APIData”。
Object literal 只能指定已知的属性，类型“APIResponse & APIData”中不存在“wahhhhh”。**。这表明**‘wahhhh’**属性显然在两个接口中都不存在。

[![Right Code - Intersection](img/d38c19ce906a1bd1b505a3685d439f7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jKEzoHpU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sqfao4wx05p1dnpdtpxy.png)

但是如果我们将**“wahhhhhh”**改为**“产品”**，编译时错误将会消失。

感谢各位阅读这篇文章。

## 过得愉快😃！。