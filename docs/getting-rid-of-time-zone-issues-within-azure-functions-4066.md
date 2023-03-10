# 消除 Azure 函数中的时区问题

> 原文：<https://dev.to/azure/getting-rid-of-time-zone-issues-within-azure-functions-4066>

您的客户希望每天凌晨 2 点运行数据库清理任务。为什么？主要是因为这是在网站没有流量的时候，它降低了有人因维护任务而遇到问题的风险。

听起来不错！作为一名工程师，您知道该任务是周期性的，您不需要创建虚拟机来处理该任务。甚至不需要应用服务。你可以不用服务器，并从他们提供的每月 100 万次免费执行中受益！哇哦。免费维护任务！

你创建一个新的函数应用程序，写一些类似这样的代码。

```
[FunctionName("DatabaseCleanUpFunction")]
public static void Run([TimerTrigger("0 0 2 * * *")]TimerInfo myTimer, ILogger log)
{
    // todo: clean up the database
} 
```

哇！那很容易！您将该应用程序发布到云中，手动测试几次，然后发布到生产环境中，然后回家。

当您第二天回到办公室时，您意识到作业已经运行是的...但是到了晚上 10 点。不是凌晨 2 点。怎么回事！？

你受到了时区的影响。哦，那个圆滑的时间罪犯。

## 默认时区

Azure 函数的默认时区是 UTC。因为我在东部时区，这个以前的函数现在在晚上 10 点运行，而不是凌晨 2 点。如果当时有用户在使用我的应用程序，我可能会严重影响他们。

这可不好。

## 修罗

有两种方法可以解决这个问题。第一，我们更改触发器的 CRON 表达式，用 UTC 表示时间，并保持文档更新。

第二种方法是告诉 Azure 函数应该在哪个时区解释 CRON 表达式。

对于我这样一个拥有最佳海岸的人来说，这涉及到将`WEBSITE_TIME_ZONE`环境变量设置为`Eastern Standard Time`。如果你在较小的海岸，你可能需要将你的设置为`Pacific Standard Time`。

然而，说实话。我们身处一个全球化的世界。你需要[列表](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10)?WT.mc_id=devto-blog-marouill#time-zones)。

在列表中找到您所在地区，在`WEBSITE_TIME_ZONE`中设置它，Azure 函数会自动为您的 CRON 表达式设置正确的时区。