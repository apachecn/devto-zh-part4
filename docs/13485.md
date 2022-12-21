# 在 Slack 中使用 PoshBot 中间件进行限速通知

> 原文：<https://dev.to/devblackops/using-poshbot-middleware-for-rate-limiting-notifications-in-slack-51n0>

最近，有人在 [PowerShell Slack](http://aks.ms/psslack) 工作区的 **#ChatOps** 频道询问是否可以使用 [PoshBot](https://github.com/poshbotio/PoshBot) 发送消息，推荐人们使用 [Slack threads](https://get.slack.help/hc/en-us/articles/115000769927-Use-threads-to-organize-discussions-) ，如果他们在(y)时间内发送了超过(x)数量的消息。我猜想他想鼓励线程对话，以减少他的松散的工作空间的混乱。这是我发给他的解决方案，我改编自一个关于限速的[栈溢出问题。我们将用来跟踪用户消息速率的方法被称为](https://stackoverflow.com/questions/667508/whats-a-good-rate-limiting-algorithm)[令牌桶](https://en.wikipedia.org/wiki/Token_bucket)算法。

## PoshBot 中间件

PoshBot 有[中间件钩子](http://docs.poshbot.io/en/latest/guides/middleware/#middleware-hooks)的概念，这是在命令处理生命周期中的某些事件期间执行定制 PowerShell 脚本的能力。这些钩子可以做你想做的任何事情。毕竟它们只是 PowerShell 脚本。如果您遵循文档中概述的约定，它们的设置非常简单，并且可以扩展我们的 ChatOps 工具的效用。

## 中间件钩子阶段

中间件可以执行六个不同的阶段。中间件可以修改收到的命令，将响应返回给后端，甚至完全丢弃消息，不允许它执行。您可以根据您的中间件在做什么来选择合适的阶段。

| 名字 | 描述 |
| --- | --- |
| 预收 | 在 PoshBot“接收”来自[后端](http://docs.poshbot.io/en/latest/tutorials/backend-development/overview/)的消息之前运行 |
| 接收后 | 在从后端“接收”消息、解析消息并与注册的 bot 命令匹配之后运行 |
| 执行前 | 在执行命令之前运行 |
| 执行后 | 在命令执行之后、响应发送到后端之前运行 |
| 预先响应 | 在响应发送到后端之前运行 |
| 后响应 | 在响应发送到后端后运行 |

## 添加中间件钩子

中间件钩子被添加到您的 [bot 配置](http://docs.poshbot.io/en/latest/guides/configuration/)中，位于名为`MiddlewareConfiguration`的属性下。对于我创建的速率限制示例，我们将使用`PreReceive`阶段，因为这个中间件旨在统计 Slack 中出现的正常消息，而不仅仅是 bot 命令。所有其他阶段在消息被解析并与 bot 命令匹配后运行**。如果我们使用任何其他阶段，我们只会测量 bot 命令的速率，而不是正常的 Slack 消息。**

在您的 bot 配置`.psd1`文件中，将以下内容添加到`MiddlewareConfiguration`属性中。根据需要调整挂钩名称和路径。

```
@{
    #
    # Other sections omitted for brevity
    #
    MiddlewareConfiguration = @{
        PreReceive = @{
            Name = 'RateLimiter'
            Path = 'C:/Users/Brandon/.poshbot/middleware/rate_limiting_notice.ps1'
        }
        # PostReceive = @{
        # Name = ''
        # Path = ''
        # }
        # PreExecute = @{
        # Name = ''
        # Path = ''
        # }
        # PostExecute = @{
        # Name = ''
        # Path = ''
        # }
        # PreResponse = @{
        # Name = ''
        # Path = ''
        # }
        # PostResponse = @{
        # Name = ''
        # Path = ''
        # }
    }
} 
```

现在让我们看看`rate_limiting_notice.ps1`并浏览每一部分。

### 限速 _ 通知. ps1

> 完整的脚本可以在这个 [GitHub gist](https://gist.github.com/devblackops/2b02fbb946a421de6efb5b9c8ce7d79b) 中找到。

PoshBot 中间件钩子只是标准的 PowerShell 脚本，但是 PoshBot 期望有两个参数可用，并将特定的对象传递给它们。

`$Context`是一个 PowerShell 对象，包含大量关于从后端接收的传入消息的信息。谁发送了消息，它在什么通道中，来自后端的原始 JSON 消息，等等。我们的中间件需要接受这个对象作为脚本的第一个参数。

`$Bot`是主 PoshBot 实例对象。它本质上是一个 PowerShell 类实例，有一堆实现所有 bot 逻辑的方法。我们的中间件可以访问这个对象，因此我们可以对 PoshBot 内部进行深度修改。记住，权力越大，责任越大。

```
<# .SYNOPSIS Suggest Slack threads for talkative users. .DESCRIPTION This middleware tracks how many messages (x) users send per (y) amount of time.
    If a user goes over the threshold, we'll send a message suggesting that Slack threads should be used. .NOTES Based on https://stackoverflow.com/questions/667508/whats-a-good-rate-limiting-algorithm
#>  param(  $Context,  $Bot  ) 
```

在下一节中，我们将告诉 PoshBot 记录一条消息，说明这个中间件钩子正在启动并定义我们的速率限制值。我们还将从上下文对象中提取调用用户 ID。我们以秒为单位定义速率限制窗口，但为了提高实际测量的精度，我们将在内部使用毫秒。

我们还需要确保不测量线程对话中已经存在的消息，或者计算 PoshBot 收到的关于线程对话更新的其他消息。如果我们不排除这些，我们的速率限制将无法正常工作，我们将纠缠人们使用线程对话，当他们已经在使用线程对话时，那将是…尴尬。

```
$Bot.LogDebug('Beginning message ratelimit middleware')  # We'll allow (5) messages per user in a (60) second window before suggesting threads  $maxMsgs  =  5  $timePeriod  =  60  $userId  =  $Context.Message.From  $timePeriodMS  =  $timePeriod  *  1000  # Only measure messages NOT already in a thread  # This middleware hook stage also receives extra messages whenever a user replies in a thread  # We need to ensure we DON'T count these against the rate-limiting  $unThreadedMsg  =  (  ([string]::IsNullOrWhiteSpace($Context.Message.RawMessage.thread_ts)  -and  ($Context.Message.RawMessage.type  -eq  'message'  -and  $Context.Message.RawMessage.subtype  -ne  'message_replied'))  ) 
```

接下来，假设我们正在处理一条**无线索**消息，我们将加载一个跟踪对象，或者创建一个新的跟踪对象(如果它不存在的话)。我们将这些数据存储为一个`CLIXML`文件，所以我们需要使用`Import-Clixml`来检索对象。这个跟踪器是一个以用户 ID 作为键的哈希表，一个包含用户当前消息允许量和他们最后一次发送消息的时间作为值的哈希表。

```
if  ($unThreadedMsg)  {  # Load the tracker  $trackerPath  =  Join-Path  $Bot.Configuration.ConfigurationDirectory  'msg_ratelimiting_tracking.clixml'  if  (Test-Path  $trackerPath)  {  $tracker  =  Import-Clixml  $trackerPath  }  else  {  $tracker  =  @{  $userId  =  @{  Allowance  =  $maxMsgs  LastMsgTime  =  [datetime]::UtcNow  }  }  } 
```

接下来，我们将获取当前时间，并确定自用户上次发送消息以来已经过去了多少毫秒。然后，该值用于计算我们的铲斗余量。

```
$now  =  [datetime]::UtcNow  $timePassed  =  ($now  -  $tracker[$userId].LastMsgTime).TotalSeconds  $tracker[$userId].LastMsgTime  =  $now  $tracker[$userId].Allowance  +=  $timePassed  *  ($maxMsgs  /  $timePeriodMS) 
```

我们现在需要查看我们的津贴，并确定我们是否违反了设定的费率限制。如果我们有`<1`余量，那么我们向 Slack information 用户发送一个友好的消息，也许他们应该使用 Slack 线程。我们通过创建一个`Response`对象来做到这一点，这个对象是 PoshBot 内部的一个类，它表示我们想要发送到后端聊天网络的一条消息。然后我们重置用户的限额，这样我们就不会继续发送此消息，除非他们再次违反限制。最后，我们将使用`Export-Clixml`将这些数据保存回磁盘。

```
if  ($tracker[$userId].Allowance  -lt  1.0)  {  $Bot.LogDebug("User [$userId] has breached ratelimit of [$maxMsgs] messages in [$timePeriod)] seconds. Sending thread reminder response")  $response  =  [Response]::new()  $response.To  =  $Context.Message.To  $response.MessageFrom  =  $Context.Message.From  $response.OriginalMessage  =  $Context.Message  $mentionUser  =  "<@$($Context.Message.From)>"  $text  =  "Hey $mentionUser, we noticed you have a lot to say. Perhaps creating a Slack thread would be useful."  $response.Data  =  New-PoshBotTextResponse  -Text  $text  -AsCode  $Bot.SendMessage($response)  $Bot.LogDebug('Sending thread reminding response')  # Reset so we don't send again until they breach the limit again  $tracker[$userId].Allowance  =  $maxMsgs  }  else  {  $tracker[$userId].Allowance  -=  1.0  }  $tracker  |  Export-Clixml  -Path  $trackerPath 
```

最后，我们将关闭上面的 *if/else* 语句，如果我们根本不需要测量这个消息，就记录一个调试消息。然后，我们将命令上下文返回给 PoshBot。将`$Context`对象返回给 PoshBot 告诉它继续执行任何其他中间件钩子。如果我们想告诉 PoshBot 停止处理这条消息，我们不会从脚本中返回任何内容。

```
}  else  {  $Bot.LogDebug("Ignoring message. It's already in a threaded conversation.")  }  # Return context back for any subsequent middleware  $Bot.LogDebug('Ending message ratelimit middleware')  $Context 
```

## 总结

> 完整的脚本可以在这个 [GitHub gist](https://gist.github.com/devblackops/2b02fbb946a421de6efb5b9c8ce7d79b) 中找到。

我希望这篇文章内容丰富，突出了 PoshBot 中间件钩子的强大功能和灵活性。我确信这个脚本没有考虑到一些边缘情况，甚至可能包含一两个 bug，但是我将把它留给读者作为练习。

干杯