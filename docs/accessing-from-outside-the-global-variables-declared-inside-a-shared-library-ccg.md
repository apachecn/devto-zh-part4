# 从共享库中声明的全局变量外部访问。

> 原文：<https://dev.to/vg283/accessing-from-outside-the-global-variables-declared-inside-a-shared-library-ccg>

我有一个简单的 c 文件(mylib.so)在 Mac 上编译成了. so。这个文件有两个全局变量，一个是整数，另一个是指向堆的指针。从外面。所以，也就是..从另一个文件(myapp.c)中，我看到我可以访问。但是当我试图访问这个指针时，它的值为空。我在 myapp.c 中将两者都声明为 externs，我想知道为什么全局指针为空？