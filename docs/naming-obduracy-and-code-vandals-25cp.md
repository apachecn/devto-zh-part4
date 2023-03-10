# 命名顽固和代码破坏者

> 原文：<https://dev.to/juricakenda/naming-obduracy-and-code-vandals-25cp>

当面临清理别人的代码甚至我们自己的代码的挑战时(可能写得如此糟糕以至于让我们感到苦恼)，我们通常热衷于保留大部分代码的现状，只修改绝对最小的所需代码。这一切都始于保持命名的原样，而不是从“不够低”开始。让我解释一下为什么这是一个巨大的错误。

我们都见过他们。代码中的 is，js 和 ks。那些微小的、毫不相关的变量名，缓慢但肯定地在它们的作用域内出错。当清理代码时，我们很可能会把它们当作一个问题忽略掉，然后转移到更感兴趣的东西上。但这正是雪球开始形成的地方。如果我们允许的话。很快我们遇到一个名为 flag 的变量，然后跟随一个 flag2 或者一个完全成熟的对象，方便地命名为- sb。再多几行这样的代码，看起来就像一堆不可读的、令人难以置信的谜题。编程只是变成了一份工作。

虽然你可能会尝试让一些小东西比如循环计数器像 I 一样滑动，但是要注意你正走向一个滑坡。这个变量名对你没有帮助。您应该从计数器变量开始清理命名，并向上传播到类名，如果需要的话甚至是文件名。

为什么从柜台和标志开始？

我想提一个关于预防犯罪的著名理论——破窗理论。对于那些不熟悉这一理论的人来说，这一理论认为景观可以与人交流。破窗发出的信息是，社会缺乏控制，对犯罪行为毫无防备，容易受到犯罪行为的伤害。最大的问题是，它显示了内部人员缺乏凝聚力。

问题可能不在于那扇破窗。它没有造成太大的伤害。但是当越来越多的窗户被打破时，因为那一扇窗户没有被固定。最终，破坏者将开始闯入大楼并破坏其内部。不需要太多时间，整个建筑就会呈现出难以想象的形状。

很明显这个理论也适用于代码。窗户是我们的命名惯例，建筑是我们的类和代码库，不幸的是，如果我们听之任之，我们就会成为破坏越来越多窗户的破坏者。

如何防止代码侵蚀？

真正防止这种情况发生的唯一方法是实施零容忍政策。你不能说像 wordCount 这样的描述性名称不如 I 可读性强。在一个极短的循环中，这看起来像是不必要的混乱，但事实并非如此。它准确地告诉我们它在做什么，我们永远不需要重读代码来理解它在做什么。当考虑一个合适的名字时，我们应该考虑这个名字如何增加代码的可读性。

它支持破窗还是零容忍政策？

对于每一件你命名的事物，问自己这个问题，没有例外。规则的每一个例外都是你建筑上的一扇额外的破窗户。不要做代码破坏者。