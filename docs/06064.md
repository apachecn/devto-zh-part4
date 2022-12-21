# 在 Sidekiq 队列中分发作业的简单方法

> 原文：<https://dev.to/jetrockets/a-simple-way-to-distribute-jobs-in-sidekiq-queues-4gh7>

此选项意味着一个上下文的作业在一个队列中顺序执行，而不同上下文的作业在不同的队列中并行执行。

让我们看看下面的例子。
我们希望对一些投资基金进行耗时的报告计算。同一基金内的计算作业是按顺序执行的，因此计算中没有错误，不同基金的作业是并行执行的。

我们自动分配队列中的作业，以便不用手动指定队列。
指定`sidekiq.yml`配置文件中的队列:

```
:queues:
  - fund_processor_0
  - fund_processor_1
  - fund_processor_2
  - fund_processor_3
  - fund_processor_4 
```

Enter fullscreen mode Exit fullscreen mode

重要的是队列从 0 开始编号。

现在，当启动 worker 时，我们指出将根据基金将作业设置在哪个队列中。为此，我们使用的操作是将基金 ID 和队列计数相除得到余数。所以我们得到了队列号。

```
# 5 queues
# fund ID % count of queues = queue number
# 1 % 5 => 1
# 2 % 5 => 2
# 3 % 5 => 3
# 4 % 5 => 4
# 5 % 5 => 0
def queue_name(fund_id)
  queue_number = fund_id % 5
  "fund_processor_#{queue_number}"
end 
```

Enter fullscreen mode Exit fullscreen mode

启动 worker，向他指示收到的队列名称。
这可以使用[Sidekiq API](https://www.rubydoc.info/github/mperham/sidekiq/Sidekiq%2FClient:push)T3 来完成

```
Sidekiq::Client.push(
  'queue' => queue_name(fund_id),
  'class' => Fund::ReportCalculator,
  'args' => [fund_id]
) 
```

Enter fullscreen mode Exit fullscreen mode