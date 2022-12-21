# Haskell 中的一元绑定是什么意思？

> 原文：<https://dev.to/niinpatel/what-does-the-phrase-monadic-bind-mean-in-haskell-ie7>

我已经学习 Haskell 几个月了，我想我对单子有了很好的理解。我不是专家，所以我应该警告你这个答案可能完全错误。

我最初是以 Quora 答案的形式发布这个帖子的。转贴于此:

**一元绑定**是(> > =)函数或绑定函数的名字，也称为链、平面图或连接图。

我个人喜欢称它为“then”函数，这个词来自 JavaScript Promises。如果你把它理解为“那么”而不是“绑定”，那会更直观。

考虑下面的 Haskell 程序，它从 stdin 中获取一个数，并在 stdout 中输出它的平方根。