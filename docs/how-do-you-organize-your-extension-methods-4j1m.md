# 你如何组织你的扩展方法？

> 原文：<https://dev.to/peledzohar/how-do-you-organize-your-extension-methods-4j1m>

你有所有扩展方法的类库吗？
你从一个项目复制&粘贴扩展方法到另一个项目吗？
你对不同的扩展对象有不同的类库吗(比如`stringExtensions.dll`、`IComparebleExtensions.dll`等)？

就我个人而言，我发现每个程序集最好有一个不同的 dll——例如，我将在一个名为`<companyName>.System.Extensions.dll`的 dll 中有一个`StringsExtensions`类和一个`IComparebleExtensions`类。

你喜欢什么？你为什么喜欢那样？