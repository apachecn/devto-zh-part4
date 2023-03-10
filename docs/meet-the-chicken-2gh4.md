# 见见这只鸡

> 原文：<https://dev.to/vicentemaldonado/meet-the-chicken-2gh4>

[![](img/8cf65db5f48c263dba78a402f6654f0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9Bsl3Lt7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArWhFhPhZd4qCsNBZQJ-0ig.jpeg)

不，不是他。你看，一开始，有鸡。菲利克斯人说:“要有蛋！”———还有鸡蛋。

CHICKEN 是编程语言方案的一个变种。是的，有很多愚蠢的括号的那个。不，不是那个，那是 Lisp—**L**ost**I**n**S**tupid**P**arenthesses。Scheme 甚至更加陌生，信不信由你，它甚至更不可用。或者是？

如果你访问了 CHICKEN 网站，你会发现它力求简单、可移植、可扩展、文档完善并得到积极支持。嗯，让我想想:

*   简单的，简单的。我能够用一个命令在我的 Linux 机器上安装 CHICKEN。在 Windows 上，我不得不下载源代码。)编译它以得到鸡肉——这相当于 6 — 7 次鼠标点击和差不多同样多的键盘敲击。如果您遵循自述文件，从源代码编译 CHICKEN 是相当容易的。此外，CHICKEN 最近已经升级到第 5 版，并且有三键安装程序可用于以前的版本(4)，所以可以相当安全地假设当前版本也会弹出一些。**耐心点**。

编辑。啊，Emacs，所有生命问题的原因和解决方法。是的，Emacs 仍然是 paren 语系的最佳选择。您可以折衷使用 VS Code、Eclipse、JEdit 或许多支持 Scheme 语法突出显示的文本编辑器。有点减号——反正没有太多语法。

CHICKEN 附带了几个可执行文件:“csi”是 CHICKEN Scheme 解释器，它启动了 REPL a 循环，所有口齿不清的人都喜欢这个循环，但很少有人能解释为什么。“csc”是 CHICKEN Scheme 编译器——它生成本机可执行文件。是的，你可以用 CHICKEN 制作独立的可执行程序。**这是大**。‘CHICKEN-install’—你可以用那个来安装外部的鸡库(当然它们被称为“Eggs”)。chicken-install 从 chicken 的中央存储库中“取出鸡蛋”源代码，编译后供您使用。这在大多数时候都很有效，除了一些 egg 有外部依赖(例如 SDL 图形库),您必须在安装有问题的 egg 之前自己安装。还要注意,' csc '与 C Sharp 编译器的' csc '混在一起，所以' csi '和' csc '有可能被称为' chicken-csi '和' chicken-csc ',所以就有这种情况。

总之，只要你做一些工作，CHICKEN 就足够简单了——我猜和其他编程环境差不多。

*   **可扩展**。鸡蛋。鸡肉最好的部分。

需要图形用户界面吗？[鸡肉(几乎)覆盖了你](http://eggs.call-cc.org/5/#ui)。最受欢迎的小鸡图形用户界面库似乎是 IUP(由 Lua 的人制作的),但是由于我前面提到的依赖性，我还不能在我的 Linux 上安装它。在 Windows 上，CHICKEN 在之前的版本中确实预装了 IUP(4)。另一方面 Tk (Tkinter，Python，你好！)绑定工作得非常好，如果你觉得太受限制，你总是可以使用 Java Swing(可能还有 JavaFx，但我还没有尝试过)。

想做一些网站开发吗？太可怕了。除了 Hello World，我还没做过什么可怕的事情，但它确实存在，而且很有效。

图形编程？[没问题](http://eggs.call-cc.org/5/#graphics)！数据库？[还是没问题](http://eggs.call-cc.org/5/#db)！网络？…你明白了——见鬼，j [你自己看看这份名单吧](http://eggs.call-cc.org/5/)——令人印象深刻。

嗯，CHICKEN **是**可扩展扩展的。请记住，这是一个为他们自己的需要而建立图书馆的小社区——你正在寻找的东西可能正是你所缺少的。

*   **积极支持**。如果是那样的话，问问他们自己就知道了。这里有一个[邮件列表叫做小鸡用户](http://wiki.call-cc.org/discussion-groups)。该列表并不活跃，但反应非常迅速——我最近在那里贴了几个问题，并收到了一个答案——可以说是直接来自孵化鸡蛋的人。还有一个 IRC 频道，我没去过。此外，如果你在 Stack Overflow 和 Reddit(在 r/lisp 和其他地方)上遇到来自 CHICKEN 团队的人，不要惊讶
*   **有据可查**。那是保守的说法。有:

[**一维基**](http://wiki.call-cc.org/)

[**一本入门指南**](https://wiki.call-cc.org/chicken-for-programmers-of-other-languages)

[T1】教程 T3】](http://wiki.call-cc.org/tutorials)

[**用户手册**](http://wiki.call-cc.org/man/5/The%20User%27s%20Manual)

[**小窍门**](https://wiki.call-cc.org/tips%20and%20tricks)

[**鸡蛋**](http://eggs.call-cc.org/)

…以及更多。

总而言之，强烈推荐鸡肉。看看这个小家伙:

[![](img/3785aabc2568149fdb4c2a786e307298.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t-bxRHZv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/335/1%2AE02BxkAHshSB2ucrm850GA.png)

ttfn！