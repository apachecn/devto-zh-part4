# C#中 Yield 语句的基准测试

> 原文：<https://dev.to/mokenyon/benchmarking-the-yield-statement-in-c-2d8g>

最近，我对我每天使用的 C#特性的基准测试很感兴趣。我发现我的很多想法都是基于假设而不是事实。今天我将深入 C#中的 Yield 语句，看看它与显式创建枚举有何不同。

通过[项目的 Github repo](https://github.com/MorganW09/BenchmarkExamples) 找到代码，查看完整的基准并自己运行。

# BenchmarkDotNet

获得准确有效的基准测试结果并不容易。在我职业生涯的早期，如果我想获得一个“基准”，我会求助于手动计时。但这是一种非常幼稚的基准测试方法。幸运的是，有一个名为 [BenchmarkDotNet](https://github.com/dotnet/BenchmarkDotNet) 的很棒的库，它让基准测试变得简单，并为您完成所有困难的事情。

查看他们的网站或者我写的一篇介绍图书馆的文章。

# 收益率报表

那么什么是收益率声明呢？微软的文档说:

使用 yield 定义一个迭代器，当你实现定制集合类型的 IEnumerable 和 IEnumerator 模式时，就不需要显式的额外类(保存枚举状态的类，参见 IEnumerator 的例子)。

看一下下面的例子: