# 实体属性:公共的、受保护的还是私有的？

> 原文：<https://dev.to/juyn/entities-properties-public-protected-or-private-78m>

我们都写了很多模型和实体，特别是当我们使用像 Symfony 这样的框架时。

但是，当你对它们编码时，你必须对属性做出选择。它们应该是公共的、受保护的还是私有的？

Symfony 做出了选择:私有。
Laravel 选择了 public。

我的同事，在我们的 Symfony 项目中，用一堆访问器将它们编码成受保护的或私有的。

我更喜欢 Laravel 的方式，并且总是用公共可见性来编码它们。

我做出这样的选择是因为大多数时候(99%的时候)，**访问器是无用的**，它们不转换数据。我更喜欢用`object->property`而不是`object->getProperty()`来访问它们

而你，你的默认选择是什么能见度，为什么？