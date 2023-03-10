# 数据访问中的反模式，第 2 部分——嵌套选择

> 原文：<https://dev.to/vbilopav/antipatterns-in-data-access-part-2-nested-selects-ngl>

在数据访问系列文章反模式的第一部分- [中，我讨论了内存连接反模式。](https://dev.to/vbilopav/antipatterns-in-data-access-part-1-memory-joins-31o4)

嵌套选择反模式非常相似，因为它是另一种尝试(通过不同的方式)——在客户机上执行数据库连接。

> 数据库连接需要在数据库中执行。

## **分歧都差不多:**

*   系统性能严重不足，或者说得好听一点，性能不佳。

*   受损的垂直可伸缩性——系统中的数据越多，性能越差——直到达到临界点(用户无法接受的响应时间)。

*   修复需要对应用程序的大部分进行非常危险、乏味和困难的重写(通常在非常有限的时间范围内，因为用户的紧迫性会导致没有人需要或想要的高度紧张)。

## * *那么是什么呢？

嗯，它试图连接客户机上的数据，来自两个不同的查询，最好用下面的伪代码来描述:

```
for each row from outer query:

    for each row from inner query:

        ## join results to new data structure from query 1 row and query 2 row

    end loop

end loop 
```

又称为 **N+1 查询问题**，因为它生成 N+1 个查询——一个针对初始外查询，N 个针对初始外查询的 N 个结果——等于 N+1 个查询。

## 那么为什么这么慢呢？

这是因为您需要执行一个 N+1 查询，其中 N 可以是数百或数千个查询，有时甚至更多。每个查询在数据库上执行后将返回数据，加上您的网络响应时间(或延迟)乘以查询数量，再加上带宽和数据库执行时间。

**网络响应时间(或延迟)是这里的主要因素。**不像以前的反模式(内存连接)那样，带宽是瓶颈——这完全是关于延迟。执行这种类型操作的最短时间是延迟* (N + 1)，其中 N 是查询的数量。您仍然需要处理带宽和数据库执行，这在这里甚至是次要因素。

所以它确实很慢，而且无论你如何索引你的数据库(它通常被过度索引)，它仍然会很慢。不仅如此，由于背后有大量不必要的代码，它迟早会被紧急重写。

然而，以我的经验来看，不管什么原因，这都是一种非常非常常见的方法。通常，没有接受过数据库开发指导或培训的初级开发人员负责构建业务应用程序。有时甚至有经验的开发人员会用特定的方式工作。

整个企业系统完全通过使用此反模式构建。许多可搜索的数据网格，许多报告——都是用这种反模式构建的。非常慢，处于可用性的边缘，很难或不可能扩展，甚至更难正确重写。

在我职业生涯的开始，我被教导要尽一切办法避免这种反模式。而且当时看起来像是常识和常识。今天，至少对我来说，这似乎是一种普遍接受的方法，即使是在非常严肃的公司里。硬件变得更强了，所以不太明显...开始时，数据很少。

在我看来，最好还是从一开始就完全避免这种反模式。

你觉得怎么样？你同意我的观点吗？你注意到这个反模式了吗？你认为它是反模式的吗？让我知道你的想法。

下一集，我将讨论一个非常常见的数据访问反模式——优先选择。