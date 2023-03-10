# 如何使用 AWS Lambda 调度任何任务

> 原文：<https://dev.to/byrro/how-to-schedule-any-task-with-aws-lambda-3617>

您知道吗，使用 AWS Lambda 可以调度几乎任何任务在云中运行？

我已经被使用 [Dashbird](https://dashbird.io/?utm_source=dev.to&utm_medium=referral&utm_campaign=article&utm_content=tutorial) 的人问过几次怎么做了，所以我想着总结在一篇文章上，供大众参考。

事件驱动的方法可能是 AWS Lambda 最强大的方面。简而言之，Lambda 可以自动响应 AWS 内部或外部的多种事件。

这种行为可以用作任务调度器，这就是我们将在本文中讨论的内容。

# DynamoDB TTL

[DynamoDB](https://aws.amazon.com/dynamodb/) 就像数据存储的 Lambda:无服务器、高可伸缩、完全托管、无 SQL 数据库。

它有一个很酷的功能，叫做 [TTL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html) ，代表“生存时间”。TTL 允许我们为数据库中任何条目的删除分配一个时间戳。到那时，DynamoDB 中的后台作业将自动为我们删除该条目。

另一个有趣的特性是 [table streams](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html) : DynamoDB 将跟踪对特定表中的条目(DB 条目)所做的每个更改，并生成描述这些更改的对象流。这些流可以由各种来源使用，包括...你猜对了，拉姆达！

[![Alt Text](img/e819a1b14c4cba6ef7661d6a5875322a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bP-T6hoh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4jgrdr3v3b5dqms5zg8c.PNG)

这两个特性[可以结合起来](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/time-to-live-ttl-streams.html)将 DynamoDB 转换成一个任务调度器。这是如何工作的:

首先，创建一个带有 TTL 的项目

```
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('MyTable')

table.put_item(
   Item={
        'customerID': 'ABC123',
        'task': {
            'action': 'alert_expiration',
            'target': 'subscription',
            'args': {
                'subscriptionID': 'XYZ123'
            }
        },
        'ExpirationTime': 1609416000
    }
) 
```

在上面的示例中，我们设置了 2019 年 12 月 31 日的时间表，提醒客户订阅即将到期。

请注意我们为该项设置的`ExpirationTime`属性。DynamoDB 会不断地将表中条目的`ExpirationTime`与当前时间戳进行比较。物品过期，迪纳摩会删除。

当项目被删除时，DynamoDB 流将触发我们的 Lambda 函数。我们可以选择请求 Dynamo 提供被删除条目的内容，以便 Lambda 知道该做什么。

在 Lambda 代码中，我们需要实现处理任务的逻辑。在本例中，它可以使用预先确定的模板向客户发送电子邮件。

> 重要提示:Dynamo 扫描您的项目并删除过期项目的频率可能会在 48 小时内变化。这是相关的，因为如果您的用例需要精确的时间分辨率，上面的实现是不推荐的。

# S3 对象到期

[S3](https://aws.amazon.com/pt/s3/) 就像对象存储的 Lambda。它可以可靠地存储从文本文件到图像、视频等内容，按存储桶进行组织，并根据需求无缝扩展。

[![Bucket](img/a027419e61e2de36f329f9c8a31c4efe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QT5kV_cT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eibxt4xe25qkukys329u.PNG)

使用 S3 作为 AWS Lambda 的任务调度程序与 DynamoDB 流非常相似。我们可以为一个对象设置一个截止日期。S3 会定期扫描我们的对象，删除过期的。

S3 中的事件，比如对象删除，[也可以触发](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html)一个 Lambda 函数，类似于 DynamoDB。

我们可以在 S3 上存储一个 JSON 文件，其中包含 Lambda 函数在需要时处理它的指令。

它是这样工作的:

```
import json
from datetime import datetime
import boto3

s3 = boto3.client('s3')

data = {
    'customerID': 'ABC123',
    'task': {
        'action': 'alert_expiration',
        'target': 'subscription',
        'args': {
            'subscriptionID': 'XYZ123'
        },
    }
}

s3.put_object(
    Body=json.dumps(data),
    Bucket='my-task-scheduler',
    Key='test-scheduler.json',
    Expires=datetime(2019, 12, 31)
) 
```

同样，请注意`Expires`属性，它设置对象何时应该删除。

当这个对象被实际删除时，S3 将调用我们的函数，提供桶名和对象键。有了这些信息，我们的 Lambda 可以从 S3 检索对象内容，读取任务指令并执行我们想要的逻辑。

> 重要提示:与 DynamoDB 一样，AWS 不保证对象在到期后立即被删除，这使得该实现不适合需要精确时间分辨率的用例。

# 云观察规则时间表

[![Watch](img/0c5398dcd44128bf23abc6a5abb6ca83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sabQCvAj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hemo8sv9brp7tu8x3nzv.PNG)

CloudWatch 可能是 Lambda 任务调度最明显的选择，而且再简单不过了:选择一个“Schedule”作为事件源，选择一个 Lambda 函数作为“Target”:

不过，主要有两个缺点:

### 1)仅支持周期性计划

我们只能选择一个固定速率的时间，例如“每 2 小时”，或“每 5 分钟”，或类似于 [CRON](https://en.wikipedia.org/wiki/Cron) 的表达。

不可能为事件的发生安排一个具体的日期，例如，我们对 DynamoDB 和 S3 所做的那样。

### 2)没有定制事件

CloudWatch 规则调度对 Lambda 的每次调用都将提供完全相同的请求负载。我们不能仅仅根据调度程序提供的东西来定制 Lambda 的运行方式，就像我们对 DynamoDB 和 S3 做的那样。

[![CloudWatch Rule](img/b0ccaadbaa29174b7ce5d3b2d3a636db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5C4aj3_w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xsd0xkyqkapxpu5e2nh7.png)

# 包装完毕

[![Wrap Up](img/bd3dca8a3dbb819bf917be2b099af94f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IY_xw_wj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k16oz0v9h61ctq6agwp7.PNG)

我们已经探索了三种使用事件驱动的方法来调度 AWS Lambda 要处理的任务的方式，每种方式都有其优点和缺点。

使用这种架构有一个挑战:保持对系统中正在发生的事情的控制和可见性。自治调用可能很难跟踪，尤其是当应用程序开始扩展时。

如果您想在获得无服务器事件驱动架构优势的同时保持领先，我建议您查看一下 [Dashbird](https://dashbird.io/?utm_source=dev.to&utm_medium=referral&utm_campaign=article&utm_content=tutorial) ，这是一款专为您的用例设计的监控服务。

*披露:我是 [Dashbird](https://dashbird.io) 的开发者代言人。*