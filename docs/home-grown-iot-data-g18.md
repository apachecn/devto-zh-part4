# 国产物联网数据

> 原文：<https://dev.to/azure/home-grown-iot-data-g18>

我的物联网[解决方案设计](https://dev.to/azure/home-grown-iot-solution-design-io5)的一部分，我想在数据方面深入一点，因为毕竟，我做这个项目的原因是捕获数据。

在制作物联网项目时，你可能要问自己的第一个问题是，你将在哪里存储数据，这是我开始的地方，但现在我已经完成了第一个版本，我意识到这是一个错误的问题，相反，我应该问我将如何处理数据？

## 使用您的数据

在选择存储类型和数据结构之前，开始考虑数据的用途是非常重要的。Azure 有很多东西可供选择，如[时间序列洞察](https://azure.microsoft.com/en-us/services/time-series-insights/?WT.mc_id=aaronpowell-blog-aapowell)、[机器学习服务](https://azure.microsoft.com/en-us/services/machine-learning-service/?WT.mc_id=aaronpowell-blog-aapowell)、[流分析](https://azure.microsoft.com/en-us/services/stream-analytics/?WT.mc_id=aaronpowell-blog-aapowell)、 [Power BI](https://azure.microsoft.com/en-us/services/power-bi-embedded/?WT.mc_id=aaronpowell-blog-aapowell) 甚至是简陋的 Excel 电子表格！你也可以建立自己的仪表盘，甚至可以用 React 制作一些[动画图表😉。](https://dev.to/azure/react-svg-chart-animation-5dfh)

所有这些都会影响您对存储和结构的决策。

对于我的解决方案，我有两种方法可以使用当前的数据*，第一种是生成功率 BI 报告，让我可以查看一段时间内的趋势(发电、消耗等)。)，其次是创建一些自定义的实时图表。*

## 结构化您的数据

现在，我们已经知道了在没有数据的情况下我们想要做什么，是时候考虑如何构建数据了，因为这将对我们使用的存储类型产生影响。

从我的逆变器中，我得到了三组数据:

1.  受监控传感器的标签
2.  来自每个受监控传感器的值
3.  以 5 分钟为增量产生的电力(我认为…我不能 100%确定是不是这样，但我认为是这样的)

1 和 3 中的数据很有趣，但真正有价值的信息是来自第二个 API 的数据。在这里，它被分成几个有价值的组:

*   每个面板组的瓦特、安培和伏特数
*   输入逆变器的瓦特、安培和伏特的汇总
*   进入电网的瓦特、安培、伏特和赫兹的汇总(我想…它在 API 中被称为“输出”,所以我猜它是从逆变器到电网的)
*   日、周、月、年和所有时间的总生成摘要

还有一堆我也不理解的其他要点(这毕竟是一个未记录的 API🤣).

考虑到这一点，我开始考虑我将为数据制定的各种“问题”，例如“在过去的 30 天里，每个面板集产生的电力是多少？”、“总进出是多少？”或者“我们一个家庭用多少电？”。这帮助我思考如何最好地组织数据。

我决定原封不动地存储原始消息，因为我还没有使用所有的字段(但将来可能会使用)，并且我希望对我调用的每个 API 都这样做。

接下来，我想把主要的分成几组，面板馈送和总结。这就是我在[解决方案设计](https://dev.to/azure/home-grown-iot-solution-design-io5)中描述的使用多种功能和消费者群体的地方。

最后，我们希望为我们希望对其执行的查询类型构建我们的数据。我决定以非关系的方式为 *read* 进行优化，这意味着我将跨不同的结构复制数据，而不是进行连接。但是我仍然希望每条数据之间有一个松散的关系，因此我生成了一个关联 ID，附加到消息中，这样如果我需要的话，每条记录都可以与*相关联。*

## 选择我们的存储类型

因此，让我们来评估一下，我们希望以非关系方式存储六种不同的数据结构，并提供一些基本的查询支持。哦，我希望它便宜(嘿，这是我每个月的信用卡！).带着这些想法，我登上了蓝色的桌子。

因为我使用 F#我有代表不同结构的[记录类型](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/records?WT.mc_id=aaronpowell-blog-aapowell):

```
type PanelInfo =
     { [<PartitionKey>] Panel: string
       [<RowKey>] Id: string
       MessageId: string
       Current: float // Iin#
       Volts: float // Vin#
       Watts: float // Pin#
       MessageTimestamp: DateTime // SysTime
       CorrelationId: string } 
```

Enter fullscreen mode Exit fullscreen mode

[*来源*](https://github.com/aaronpowell/sunshine/blob/3369c32d7f657ba1b4c80650a8534a97738edc81/src/Sunshine.Functions/PanelTrigger.fs#L14-L22)

在这里你会看到我有一个`CorrelationId`属性，这允许我跟踪面板记录(每条消息有 2 个)直到它被发送时的完整数据集。我还有一个邮件的时间戳，可以让我按时间对它们进行分组。

对于我的每个数据结构，我使用不同的表，而不是组合表。这主要是为了当每个记录的结构不同时，我可以查看单个类型，而不会发现数据缺口。

### 这不完美

值得注意的是，这并不是一个完美的解决方案。当我开始研究 BI 报告的强大功能时，我的朋友 [Dom Raniszewski](https://twitter.com/DominikRan) 问我为什么使用 Table 而不是 Blob，这样效率会更高。他是对的，在 Power BI 中读取数据的存储方式存在许多低效之处，但原因是我还需要一个简单的编程模型，以便我可以构建自己的实时报告(Power BI 每 24 小时更新一次数据)。我打算暂时保持原样，但我们会看到，我可能会在未来修改它。

事实证明，未来就是现在，在写这篇文章的时候，我意识到我存储数据以供检索的方式存在设计缺陷。因为我想要的主视图是日视图，而不是秒视图(我在秒视图中捕获的)，所以我需要某种方式来查看它。但是我不能这样做，因为日期是秒的时间戳，而且表存储的查询引擎不够先进。我将仔细考虑如何最好地解决这个问题，并将其改造回我已经存储的 30k+记录中！

## 结论

数据通常是物联网项目的基石，确保您有正确的方法来存储数据将极大地提高您从中获得的好处。作为一名技术人员，您可能会立即想到选择正确的数据库类型，然后决定如何将您的应用程序融入其中。

相反，我会鼓励你改变方向，开始思考你想用你的数据做什么，然后找出最适合的方法。

对我来说，桌子储物是最合适的，原因有很多，但仍然有不完美的边缘，我必须处理。