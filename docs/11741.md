# 如何使用 mongose，首先是 mongoose find，然后是 forEach 循环，最后是 mongoose create。

> 原文：<https://dev.to/sma/how-can-i-use-mongoose-first-with-mongoose-find-then-with-foreach-loop-and-as-last-with-mongoose-create-5387>

当我在 mongoose 中使用 forEach 时，我得到一个“不能在它们被发送到客户端后设置头”的提示。我知道 forEach 没有完全执行，因为 res.send 显然已经发生。否则至少不会出现这个错误消息。

如何将 mongoose 与 forEach 一起使用，以便代码仅在 for forEach 循环完成时继续？

这是我的代码: