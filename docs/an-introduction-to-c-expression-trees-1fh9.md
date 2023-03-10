# C#表达式树简介

> 原文：<https://dev.to/mokenyon/an-introduction-to-c-expression-trees-1fh9>

表达式树是一个有趣的 C#语言特性，你以前可能没有意识到。表达式树是实体框架能够将 C#代码转换成 SQL 查询的基础。因此，如果你曾经使用过实体框架，那么你肯定利用了这种语言的特性。

比如这里是实体框架核心(EF Core)库中的 [SelectExpression](https://github.com/aspnet/EntityFrameworkCore/blob/release/3.0/src/EFCore.Relational/Query/SqlExpressions/SelectExpression.cs) 类，搜索术语 Expression 显示 is 在这个类中存在 652 次，蛮重要的！

最近我对 Entity Framework 如何将 C#转换成 SQL 很感兴趣，并想写一篇关于表达式的文章来帮助我理解这个主题。

*注意，[表达式树](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/expression-trees/)和[表达式](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/expressions)是有区别的，但是在互联网和软件中，比如实体框架，你会发现这两个词都用来描述表达式树。这让谷歌搜索变得有点混乱和困难。在本文的剩余部分，我将主要使用术语表达式。*

# 我们为什么需要表情？

大多数情况下，我们编写的代码应该在我们编写它的地方被执行。

例如，此方法用于对数字求和。