# 一个你可能想保持距离的朋友

> 原文：<https://dev.to/ankitvijay/c-dynamic-a-friend-you-may-want-to-keep-a-distance-28mk>

最近，在我的一个 PRs 被合并到 master 之后，我的队友开始抱怨一个奇怪的场景。在某些情况下，托管在 IIS 工作进程(w3wp.exe)内的 ASP.NET 核心应用程序会在没有任何异常/警告的情况下直接终止。没有明确的重现步骤，也很难确定是什么导致了这个问题。我花了一些时间来找出问题的根源，结果发现这是一个非常有趣的问题。

## 背景

作为我的 PR 的一部分，我添加了一个新代码来将一个`Repository` `object` /实体映射到一个`Dto`。这是一个非常复杂的多层嵌套`entity`。我没有使用像`Automapper`这样的库来自动映射实体。为什么我选择手动绘制地图可以留待以后讨论。不管怎样，罪犯代码是这样的。

我们有一个`abstract`基类，姑且称之为`AbsractBaseRepository`，这个类由多达 18 个不同的子类派生而来。类似下面的内容: