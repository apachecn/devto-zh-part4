# 打破 Git 2.23 中“git checkout”的垄断

> 原文：<https://dev.to/stolleydotdev/breaking-down-the-monolith-of-git-checkout-in-git-2-23-3ade>

对 Git 2.23 中的变化进行了很好的概述，GitHub.com 博客上的 care of Taylor Blau:

> Git 2.23 为现有的命令套件带来了一对新的实验命令:`git switch`和`git restore`。这两个是为了最终为众所周知的`git checkout`提供一个更好的界面。新的命令都有一个清晰的划分，整齐地划分了 git checkout 的许多职责，我们将在下面展示。

在一个已经充满挑战的套件中，长期过载的`git checkout`命令是一个非常困难的 CLI。不过，我可以很容易地想象自己被旧标志绊倒，就像在用于立即创建和检出分支的类似的`git checkout -b`和`git switch -c`命令中一样。因为`git checkout`被用在如此多不同的上下文中，所以对于每天使用 Git 的人来说，它的标志和参数模式最令人难忘。