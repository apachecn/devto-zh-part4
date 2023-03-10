# AWS 应用程序集成

> 原文：<https://dev.to/vikashagrawal/aws-application-integration-18oi>

# 步进功能

它有助于定义 lambda 函数

# 亚马逊 MQ

它是兔子 MQ 的替代品

# SNS(简单通知服务)

这是一种基于推送的服务。
可用于将通知推送到:

```
o   Mobile devices
o   SQS
o   HTTP endpoint
o   SMS text messages
o   Email
o   Lambda can be consumer of this topic and trigger another SNS or AWS services. 
```

主题:

```
o   An access point for allowing recipients to dynamically subscribe.
o   It can deliver to multiple recipients together line iOS, Android and SMS.
o   It's stored across multiple AZ.
o   The messages from this topic will be delivered to all the subscribers. 
```

# 【SQS】(简单排队服务)

它以信息为导向。它有助于与其他 AWS 服务集成。
这是一种基于拉动的服务。
队列中存储的最大消息大小为 256 KB。
消息类型可以是 XML、JSON 和无格式文本
如果产生消息的速率大于消耗消息的速率，反之亦然，我们可以使用自动缩放组，这样，如果消息多于，就会创建更多的 EC2 实例，如果消息少于，就会终止未使用的 EC2 实例。
队列中的消息可以保留 1 分钟到 14 天，默认为 4 天。
保证消息至少被处理一次。
轮询

```
o   Short: Returns immediately if no messages are in queue.
o   Long: Polls the queue periodically and only returns the response when a message is in the queue or timeout is reached. 
```

可见度超时

```
o   When the message is received by the consumer, this message gets marked as invisible in the queue. If the job gets processed by the consumer before the time out then it would be deleted from the queue else it would become visible again.
o   Default is 30 seconds and maximum are 12 hours. Any executions, which needs more than 12 hours of execution better have a lambda function to this message and split it into multiple topics and have other lambdas integrated with these topics. 
```

类型

```
o   Standard
 This is the default queue.
 Although the consumption of message is ensured based on the order they are received but nor guaranteed.
 Chances are there for any of the messages getting consumed more than 1 time. 
o   FIFO
 Consumption of message is guaranteed based on the order they are received.
 All behavior is same as default queue with only limitations is 300 Tx/sec. Reason for this limitation could be because standard is the default implementation and bit of change in the design to get FIFO design brings in this limitation.
 It will be consumed once unless it gets deleted by the consumer. 
```

# SWF(简单工作流服务)

这是一个面向任务的项目，包含以下参与者:

```
o   Workflow starters: an application that initiate the workflow, e.g. website.
o   Workers: it’s a program that gets tasks, process it and return the result.
o   Decider: it controls the coordination of tasks. 
```

域:它是一种元数据，是相关工作流的集合，存储在 JSON 中。
最长工作流保留期为 1 年，以秒为单位存储。
·SWF 代理 b/w 工作人员和决策者之间的互动。
世界安全基金会确保这项任务不再重复。
它让决策者对任务的进展有一个清晰的认识，并开始新的任务。

# SES(简单电子邮件服务)