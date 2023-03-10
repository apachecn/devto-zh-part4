# 无堆栈溢出的递归

> 原文：<https://dev.to/gabrielschade/recursion-without-stack-overflows-3c62>

这看起来像是一个艰难的成就，但实际上不是。我们将使用 F#语言和一种叫做**尾部递归**的技术。

对于许多开发人员来说，递归本身有点可怕，但这是没有理由的，它只是另一个装备的重复循环。

最终你会需要它，所以，最好做好准备。

正如我之前说过的，递归是一种重复循环技术，所以，它会帮助你解决可以分解成更小问题的问题。

然而，使用递归有一些问题。一般来说，递归循环比常规循环慢。除此之外，还有一个叫做**调用栈**的主要限制。

# 调用堆栈

在我们开始编码之前，让我们理解什么是调用栈。这个数据结构被我们的程序用来存储一个指向执行行的指针。

这个存储过程发生在每次你的程序调用一个函数(任何函数，不仅仅是递归函数)，这样，程序将能够在函数执行后返回到正确的执行行。

让我们通过创建一个简单 hello 函数来看看它是如何工作的: