# 创建预定的 Azure 功能

> 原文：<https://dev.to/pcmichaels/creating-a-scheduled-azure-function-18jj>

我曾经写过关于创建 Azure 函数的文章。我还[写过如何应对服务车排队](http://pmichaels.net/2017/07/23/using-azure-functions-send-e-mail-alert-service-bus/)的文章。在这篇文章中，我想介绍如何创建一个预定的函数。基本上，它们允许您创建一个在给定时间间隔或给定时间执行的计划任务。

# 定时器触发器

首先要做的是创建一个带有定时器触发器类型的函数:

[![](img/caebbc1efa4d9edd4c58334137b15eed.png)](http://pmichaels.net/wp-content/uploads/2018/02/schedule-1.png)

# 计划/ CRON 格式

接下来的事情是理解时间表，或 CRON，格式。格式是:

> {秒} {分} {小时} {天} {月} {星期几}

## 计划的时间间隔

当您创建它时，您将看到的示例如下所示:

```
0 */5 * * * * 
```

符号`*/[number]`表示每个数字一次；因此*/5 意味着每 5 分钟一次...然后看占位符算出 5 个什么；在这种情况下，这意味着每 5 分钟一次。比如说:

```
*/10 * * * * * 
```

会是每 10 秒一次。

## 预定时间

指定数字意味着计划将在那时执行；所以:

```
0 0 0 * * * 
```

每当小时、分钟和秒钟都归零时执行——所以每天午夜执行一次；并且:

```
0 * * * * * 
```

会在每秒钟到达零时执行，所以每分钟一次；并且:

```
0 0 * * * 1 
```

将在星期一每小时执行一次(因为最后一个占位符是星期几)。

## 时间限制

这些可以在[下限]-[上限]格式的任何列中指定，它们将计时器限制在一个值范围内；例如:

```
0 */20 5-10 * * * 
```

意味着在早上 5 点到 10 点之间每 20 分钟一次(如您所见，不同的类型可以结合使用)。

## 星号(*)

您会注意到上面每个占位符中都有星号，表示没有指定值。askerisk 表示调度将在该占位符内的每个时间间隔执行；例如:

```
* * * * * * 
```

意味着每一秒；并且:

```
0 * * * * * 
```

意味着每一分钟。

# 回到函数

启动后，该函数将详细说明下几次执行的时间:

[![](img/7d7dedf971c46b3f6ff2eb4471d01759.png)](http://pmichaels.net/wp-content/uploads/2018/02/schedule-2.png)

但是如果你不知道编译时的时间表会是怎样的呢？与 Azure 函数中的许多变量一样，您可以简单地将值替换为占位符:

```
[FunctionName("MyFunc")]
public static void Run([TimerTrigger("%schedule%")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
} 
```

然后可以在 local.settings.json 中提供该值:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "DefaultEndpointsProto . . .",
    "AzureWebJobsDashboard": "DefaultEndpointsProto . . .",
    "schedule": "0 * * * * *"
  }
} 
```

# 参考

T1】https://docs . Microsoft . com/en-us/azure/azure-functions/functions-bindings-timer

[http://cronexpressiondescriptor.azurewebsites.net/?expression = 1+*+*+*+*+*&locale = en](http://cronexpressiondescriptor.azurewebsites.net/?expression=1+*+*+*+*+*&locale=en)

原贴[此处](https://www.pmichaels.net/2018/02/11/creating-scheduled-azure-function/)。