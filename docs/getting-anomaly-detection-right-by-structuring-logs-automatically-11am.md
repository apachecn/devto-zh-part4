# 通过自动构建日志获得正确的异常检测

> 原文：<https://dev.to/gdcohen/getting-anomaly-detection-right-by-structuring-logs-automatically-11am>

[![Zebrium anomaly detection on logs](img/e840f793d0a4754ce766ed44f1ce4e41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KYzFA-EO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/anomaly.png%3Fwidth%3D580%26name%3Danomaly.png) 
可观性是指能够通过外部输出的知识来推断系统的内部状态。对于除了最简单的应用程序之外的所有应用程序，软件可观测性需要度量、跟踪和事件(例如日志)的组合，这一点已被广泛接受。对于最后一个问题，越来越多的人强烈主张提前构建日志事件。为什么？好吧，举几个原因——没有结构，你会发现自己在处理笨拙的文本索引、脆弱且难以维护的正则表达式以及被动搜索的痛苦。您理解多行事件(例如堆栈跟踪)等模式的能力，或者将事件与指标相关联(例如通过事务 ID)的能力也会受到损害。

结构化事件非常有用的另一个原因是—[它们能够实现真正有效的异常检测](https://www.zebrium.com/blog/using-machine-learning-to-detect-anomalies-in-logs)。良好的异常检测会发现“未知的未知”，这是可观测性的一个关键目标。考虑一下——如果您知道要监控哪些指标，那么您可能可以通过人工定义的阈值来实现。(注意:如果您不知道要提前监控的所有指标，智能异常检测会有所帮助——在即将发布的博客中会有更多相关内容)。如果您知道特定的诊断事件(例如特定的错误)，您可以使用明确定义的警报或签名来跟踪它们([btw 更容易使用结构化事件](https://www.zebrium.com/blog/never-troubleshoot-the-same-problem-twice))。但是，总有可能存在大量您事先不知道的情况，良好的异常检测(即，高信噪比，几乎没有误报)在这里确实很有帮助。

存在尝试对非结构化事件进行异常检测的方法。但实际上，这很难做到。或者至少做得足够好，可以日复一日地依赖。没有结构有两个基本问题——缺乏上下文和基数爆炸。

这里有一个缺乏上下文的例子。假设您的异常检测跟踪“失败”和“成功”等关键词的计数。前者不好，后者好。但是看到这种类型的消息并不罕见:“XYZ 任务没有成功完成”，与预期的含义完全相反。所以仅仅匹配关键词是不可靠的。

像“at”这样的普通词就更棘手了。在正确的上下文中，该关键字极具诊断性，例如多行堆栈跟踪中第 2 到 N 行的第一个单词。

```
 Exception in thread "main" java.lang.NullPointerException

         at com.example.myproject.Book.getTitle(Book.java:16)

         at com.example.myproject.Author.getBookTitles(Author.java:25)

         at com.example.myproject.Bootstrap.main(Bootstrap.java:14) 
```

通过了解事件结构的方法，该结构:

```
 "at \<STRING\>\(\<STRING\>:\<INTEGER\>\)" 
```

将被唯一地理解，并且它在多行日志序列中的位置将有助于诊断——例如识别 Java 异常。但是如果没有结构，关键字“at”就太常见了，仅仅基于关键字匹配的简单尝试会产生太多噪音，以至于完全没有用。因此，缺乏上下文是关键字匹配成为有用的异常检测的薄弱基础的一个原因。

另一个问题是基数。正如早期博客中的[所讨论的，我们的软件使用机器学习来自动将数千万条非结构化的日志行提取出来，形成一个更小的完美结构化的事件类型集(在相关列中跟踪类型化变量)。例如，我们的整个 Atlassian 套件有一个“事件字典”,包含 1000 多种不同的事件类型。因此，我们很容易了解每一种事件类型的正常频率、周期性和严重性，并突出显示其中任何一种事件的异常模式。寻找未知的非常有效的方法。另一方面，如果我们试图检测任意关键字组合或集群中的异常，基数要高几个数量级，这使得可靠地做到这一点是不切实际的，至少在实际的时间和资源限制内。](https://www.zebrium.com/blog/using-machine-learning-to-detect-anomalies-in-logs)

这在实践中行得通吗？你打赌！在跨越数百个数据集的真实世界测试中，我们发现我们的方法在生成异常方面证明是有效的。首先，它不会产生太多噪音:在相对简洁的日志中，只有大约 50，000 个事件中的 1 个被标记，而在数百万个事件中，有 1 个被标记为更复杂的事件。这些数据集都包含了真实的问题。

其次，它们的诊断“信号”价值很高——超过一半最终被人类标记为故障特征的根本原因已经被突出显示为异常。其余的大部分就发生在异常之前，这意味着异常挑选出了真实问题的可识别症状，节省了分类和根本原因的时间。

我们是一家早期创业公司，即将开始测试。如果您对我们的方法感兴趣，请[联系](//www.zebrium.com)。

注意:这篇文章是在作者 Ajay Singh 的许可下发表的。