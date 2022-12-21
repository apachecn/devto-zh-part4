# 用 Azure 函数支持 Slack ChatOps🤖⚡

> 原文：<https://dev.to/azure/creating-slack-commands-with-azure-functions-4j7h>

我最近一直在对 DDD 悉尼的基础设施进行升级，主要是将 API 从 Azure Functions v1 堆栈迁移到 v2(实际上是从去年开始的一项更改，由于 v2 预览版中的一个错误，我暂停了这项更改)。

一旦我完成了核心功能，我决定是时候解决我一直想要的东西了，一种我们可以查看会话信息并在我们的 Slack 通道中执行一些任务的方式。

## 延长松弛期

有几种不同的方法可以扩展 Slack，因为我的需求很简单，所以我决定使用[斜杠命令](https://api.slack.com/slash-commands)，这样我们就可以键入`/sessions 2019`并获得某一年提交的所有会话的列表。

第一步是在 Slack 中为您想要的工作区创建一个新的应用程序:

[![Creating a Slack App](img/5bc76d154f154beb3ed53f4a1dc16a17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uy77jzF_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/slack-azure-functions/001.png)

我们的应用程序已经准备好开始创建命令，但在创建命令之前，我们需要一个 URL 供它调用，为此我们将使用 [Azure Functions](https://azure.microsoft.com/en-us/services/functions/?WT.mc_id=devto-blog-aapowell) 。

## 创建我们的函数

我使用 [VS 代码创建函数](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-vs-code?WT.mc_id=devto-blog-aapowell)(但是选择你想要的编辑器)，然后我修改生成的项目，这样我就可以使用 F#(见[这篇文章](https://dev.to/azure/azure-functions-with-f-2l3c)了解你需要做什么)。

完成所有工作后，就该创建一个函数了，让我们创建一个列出已批准会话的函数！*快速提示:我们从 Sessionize 同步会议，但将它们标记为“未批准”,这意味着我们在批准它们之前会对它们进行快速审查，以确保它们没有违反我们的行为准则。只能对批准的会议进行投票。*

当执行 slash 命令时，它会向您提供的端点发送一个 HTTP POST，因此我们需要使用 [HTTP 绑定](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-http-webhook?WT.mc_id=devto-blog-aapowell)。

```
module SlackCommands

open Microsoft.Azure.WebJobs
open Microsoft.Azure.WebJobs.Extensions.Http
open Microsoft.AspNetCore.Http

[<FunctionName("Slack_Approved_Sessions")>]
let approvedSessionsCommand ([<HttpTrigger(AuthorizationLevel.Function, "post", Route = "v2/Slack-ApprovedSession")>] req: HttpRequest)
                            ([<Table("Session", Connection = "EventStorage")>] sessionsTable)
                            ([<Table("Presenter", Connection = "EventStorage")>] presentersTable) =
    ignore() 
```

让我们分解一下，我们开始创建 F#函数，这将是我们的 Azure 函数，命名为`approvedSessionsCommand`。它用`FunctionName`属性来修饰，以便函数主机知道它。最后，我们为它提供一些绑定参数，对于这个我们需要三个绑定，第一个是`HttpTrigger`属性，然后两个`Table`绑定让我们访问保存数据的表存储。对于`HttpTrigger`，它的访问级别被定义为`AuthorizationLevel.Function`，这意味着需要提供一个密钥来访问它(基本安全)，它只监听`"post"`请求，路由为`v2/Slack-ApprovedSessions` ( `v2`是因为这是 DDD 悉尼的第二代 API)。

### 处理收到的消息

当一个斜杠命令被键入时，你可以向它提供一条消息，这条消息被传递给被调用的函数。我在我们的目的中使用它来获得年份，因此该命令可以每年使用而不改变，像`/sessions 2019`一样调用它。

要获取这个文本，我们需要从传入的消息体中获取它，不幸的是，这不是 JSON 的有效载荷，而是一个标准形式的 post，所以对我们来说没有好的干净的对象。🙁

相反，我们需要把它从`HttpRequest`的身体里取出来:

```
[<FunctionName("Slack_Approved_Sessions")>]
let approvedSessionsCommand ([<HttpTrigger(AuthorizationLevel.Function, "post", Route = "v2/Slack-ApprovedSession")>] req: HttpRequest)
                            ([<Table("Session", Connection = "EventStorage")>] sessionsTable)
                            ([<Table("Presenter", Connection = "EventStorage")>] presentersTable) =
    let year = req.Form.["text"].[0]

    ignore() 
```

表单的`text`属性包含用户输入的内容(不包括斜杠命令),所以最好对其进行一些验证，以确保它符合您想要的结构，如果不符合就拒绝它。

### 获取数据

我将只快速回顾一下这个特定函数是如何获取数据的，因为它是特定于*的，我的*场景和你的场景可能不同。需要注意的是这是**只是一个 Azure 函数**，所以你可以做任何你需要做的事情。

我们的会议数据存储在跨两个表的表存储中，一个是包含会议元数据的`Session`表，另一个是包含会议发言人的`Presenter`表，因为一个会议可能有多个发言人(此外，我们不对发言人表进行重复数据删除，因此如果您提交多个演讲，我们会为您提供多个记录)。这两个表使用会话 ID“链接”在一起。

为了访问数据，我使用了 [FSharp。Azure.Storage](https://github.com/fsprojects/FSharp.Azure.Storage) NuGet 包，它为处理表存储提供了一个更好的 F# API。

```
[<FunctionName("Slack_Approved_Sessions")>]
let approvedSessionsCommand ([<HttpTrigger(AuthorizationLevel.Function, "post", Route = "v2/Slack-ApprovedSession")>] req: HttpRequest)
                            ([<Table("Session", Connection = "EventStorage")>] sessionsTable)
                            ([<Table("Presenter", Connection = "EventStorage")>] presentersTable) =
     async {
         let year = req.Form.["text"].[0]

         let! sessions = Query.all<SessionV2>
                         |> Query.where <@ fun s _ -> s.EventYear = year && s.Status = "Approved" @>
                         |> fromTableToClientAsync sessionsTable

         let! presenters = Query.all<Presenter>
                           |> Query.where<@ fun p _ -> p.EventYear = year @>
                           |> fromTableToClientAsync presentersTable
        return ignore()
    } |> Async.StartAsTask 
```

我们使用`Query.all<T>`来取回数据，然后使用`Query.where`来过滤所提供的年份和(在会议的情况下)`Approved`的状态。这不是超级优化，因为我们得到了所有演示者，即使他们与批准的会话无关，但我们谈论的是大约 100 条记录，所以性能并不真正令人担忧。

### 准备我们的应对

有了我们的数据，是时候向 Slack 发送回复了。Slack 支持[许多创建消息](https://api.slack.com/messaging/composing)的方式，但是当你使用[布局块](https://api.slack.com/reference/messaging/blocks)时，你被限制在 50 块以内，我们会有更多的对话，所以这并不理想。

相反，我们将保持简单，只使用嵌入了`mrkdwn`的纯文本响应。*注意:Slack 不使用 Markdown，它使用自己的变体`mrkdwn`。对于可以应用的格式，有一些细微的差别和限制。*

```
let sessionToViewMessage session presenters =
    presenters
    |> Seq.map (fun p -> p.FullName)
    |> String.concat ", "
    |> sprintf "(%s) _%s_ by *%s*" session.SessionizeId session.Title

[<FunctionName("Slack_Approved_Sessions")>]
let approvedSessionsCommand ([<HttpTrigger(AuthorizationLevel.Function, "post", Route = "v2/Slack-ApprovedSession")>] req: HttpRequest)
                            ([<Table("Session", Connection = "EventStorage")>] sessionsTable)
                            ([<Table("Presenter", Connection = "EventStorage")>] presentersTable) =
     async {
         let year = req.Form.["text"].[0]

         let! sessions = Query.all<SessionV2>
                         |> Query.where <@ fun s _ -> s.EventYear = year && s.Status = "Approved" @>
                         |> fromTableToClientAsync sessionsTable

         let! presenters = Query.all<Presenter>
                           |> Query.where<@ fun p _ -> p.EventYear = year @>
                           |> fromTableToClientAsync presentersTable

         let resultSessions = sessions
                              |> Seq.map(fun (s, _) ->
                                  presenters
                                  |> Seq.filter (fun (p, _) -> p.TalkId = s.SessionizeId)
                                  |> Seq.map (fun (p, _) -> p)
                                  |> sessionToViewMessage s)

         match Seq.length resultSessions with
         | 0 -> return OkObjectResult("💥 There are no approved sessions") :> IActionResult
         | _ -> return OkObjectResult(resultSessions |> String.concat "\r\n") :> IActionResult
     } |> Async.StartAsTask 
```

我引入了一个函数调用`sessionToViewMessage`,它接受一个会话及其演示者，并生成一个如下所示的行字符串:

```
(12345) _My Awesome Session_ by *Aaron Powell* 
```

这样做的结果是一个`seq<string>`，它与`\r\n`连接在一起形成一个新行，并作为`OkObjectResult`返回，以表示对 Slack 的`HTTP 200 OK`响应。

现在你的功能完成了，把它部署到 Azure(使用管道，使用 [VS 代码工具](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-vs-code?WT.mc_id=devto-blog-aapowell#publish-the-project-to-azure)等等。)并且我们准备将其插入 Slack。

## 布线起斜线命令

随着我们的函数的部署，是时候将它插入到我们的 slash 命令中了。在开始之前，确保你有 Azure 函数的 URL(你可以通过门户获得)，因为我们需要它来设置 slash 命令。

现在你已经有了我们之前创建的 Slack 应用的 URL，导航到`Features -> Slash Commands -> Create New Command`并填写表格。

[![Create New Command screen](img/4eb27a017e7aeb63d23391b6677e3456.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gAg9e_jl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/slack-azure-functions/002.png)

完成后点击`Save`。

最后，导航回`Basic Information`并确保您的应用程序已经安装到您的工作区中:

[![Install Application to Slack Workspace](img/e3b99ebae7d94fe9a6940f45098586c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2lX1Xeeh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/slack-azure-functions/003.png)

现在你的斜杠命令会出现给大家使用！

[![Slash Command in action](img/39cd5e6227761932760800b2bddce47e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hg4aeLo7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/slack-azure-functions/004.png)

🎉你现在有了一个由 Azure 函数支持的 slash 命令！

## 结论

Slack 中的 Slash 命令是将业务中的一些定制功能集成到标准工具中的一种非常简单的方法。对于 DDD 悉尼的我们来说，这意味着我们可以快速完成会议所需的管理任务，而不必深入 Azure 门户。

Azure 函数让这一切变得非常简单，从简单的 HTTP 触发器绑定接受传入的`POST`，到预解析的表单主体作为键/值对，并让函数提供我们需要集成的其他 Azure 服务的自动连接。你可以在我们的 [GitHub API 项目](https://github.com/dddsydney/dddapi/blob/master/DDDApi.Functions/v2/SlackCommands.fs)的 API 中查看所有的斜杠命令。

希望这已经让你对如何用 Slack 和 Azure 函数做自己的 ChatOps 有了一些了解。