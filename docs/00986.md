# API、ABI 和系统调用

> 原文：<https://dev.to/satoru_takeuchi/api-abi-and-system-call-19i0>

# 前言

本文简要介绍了什么是 API、ABI 和系统调用，以及这些词之间的区别。

这些话在习惯了 C 之类的低级编程语言的工程师中是众所周知的，这些语言必须考虑程序的二进制表达。然而，最近，大多数开发人员通常不知道这种知识，因为他们通常使用像 Python 这样的高级语言。

我猜 API 对大多数开发者来说就是 Web API。此外，他们还听到了 ABI 和系统调用，但不知道这些是什么。为了缓解这种情况，我会尽可能简单地解释这些单词的意思。

## 什么是 API？

API 是应用编程接口的缩写。它是源代码级别的函数(包括 OOPL 中的方法)的规范，这个函数是什么，应该设置什么样的参数，返回值是什么。它不负责这些的二进制表达。

这个例子就是我在第一节中描述的 Web API。它定义了我们应该访问什么样的 URL，以及应该设置什么样的数据来从网站获取服务。在这些日子里，API 和 Web API 都被不加区分地使用，现在我们理解 Web API 是 Web 服务的 API。换句话说，API 是比 Web API 更宽泛的词。

我将用下面的 C 源代码再展示一个 API 的例子。

```
int plus(int x, int y) {
  return x + y;
}

int main(void) {
  int a = 1;
  int b = 2;
  ..
  plus(a, b):
  ..
} 
```

在这段源代码中，plus()函数的 API 如下。

*   返回第一个参数和第二个参数的和
*   参数和返回值的类型都是 int

这里 API 不关心更多的细节，比如 int 类型是多少字节。

## 什么是 ABI？

ABI 来自应用程序二进制接口。它定义了函数和数据的二进制表达式。它定义了比 API 更详细的规范。它负责一个参数需要多少字节，以及数据应该设置在哪里，例如，堆栈的顶部或 CPU 的寄存器。在某些情况下，它还定义了数据的字节序。

如果你对 x86_64 CPU 架构有基本的了解，也可以看看下面的网址。

*   [`calling convention`维基百科页面](https://en.wikipedia.org/wiki/Calling_convention)
*   [系统 V 应用二进制接口](https://www.uclibc.org/docs/psABI-x86_64.pdf)->“3.2 函数调用序列”

例如，当从非 C 语言调用像 C 库这样的二进制库时，应该考虑 ABI

## API 和系统调用有什么区别

理解 API 和系统调用的区别有点困难。然而，现在你可以理解它，因为你知道什么是 API 和 ABI。

我将解释 POSIX C API、Linux 的 C API 和 Linux 的系统调用之间的区别。首先，POSIX 是 Unix 类操作系统的国际标准。POSIX API 定义了许多东西，比如 POSIX C API，它是访问操作系统服务的 C API。

Linux 的 C API 是访问 Linux 服务的 C API。Linux 尽量提供 POSIX C API，尽可能兼容 POSIX C API。但是，下面这些并不一样。

*   Linux 的 C API 中的一些功能与 POSIX 不兼容
*   Linux 没有提供一些 POSIX C API
*   Linux 提供了许多 POSIX 中没有的功能

系统调用是操作系统的接口。Linux 为开发者提供了许多系统调用，也提供了许多 C APIs 来使用这些系统调用。此外，从本质上讲，我们必须执行特殊的 CPU 指令来使用具有 CPU 相关系统调用 ABI 的系统调用。如果你对 Linux 的系统调用 API 感兴趣，请参考[本文档](https://www.uclibc.org/docs/psABI-x86_64.pdf)的 A.2.1 调用约定。它定义了 x86 _ 64 CPUs 的系统调用 ABI。

有趣的是，Linux 的 C APIs 中有许多函数不使用与这些函数同名的系统调用。比如 Linux 提供了`fork()`函数，但是这个函数是通过`clone()`系统调用实现的。

# 结论

这篇文章描述了以下事情。

*   什么是 API、ABI 和系统调用
*   这些单词之间有什么区别
*   POSIX C API、Linux 的 C API 和系统调用有什么区别

如今，这种知识对于许多开发人员来说并不重要。但是，总有一天，会出现你必须与底层编程斗争的情况。那么这篇文章会对你很有帮助。