# 将 F#用于表存储和 Azure 函数

> 原文：<https://dev.to/azure/using-f-with-table-storage-and-azure-functions-2daj>

我最近一直在做很多关于 Azure 函数的工作，其中我使用了 T2 表存储作为后端。这让我开始使用 [FSharp。Azure.Storage](https://github.com/fsprojects/FSharp.Azure.Storage) NuGet 包，它为表存储提供了一个更好的 API(碰巧也是我以前的一个同事写的)。

但是有一个问题，`FSharp.Azure.Storage`被设计成与`CloudTableClient`一起工作，你通常会得到这样的结果:

```
open Microsoft.WindowsAzure.Storage
open Microsoft.WindowsAzure.Storage.Table

let account = CloudStorageAccount.Parse "UseDevelopmentStorage=true;" //Or your connection string here
let tableClient = account.CreateCloudTableClient()

let inGameTable game = inTable tableClient "Games" game 
```

但是当我们使用 Azure 函数时，我们可能会在函数参数中进行绑定，就像这样:

```
[<FunctionName("Some_Function")>]
let someFunction ([<HttpTrigger(AuthorizationLevel.Function, "get", Route = "some-function")>] req: HttpRequest)
                 ([<Table("MyData")>] dataTable: CloudTable) =
    // do stuff 
```

注意，这里我们将收到一个`CloudTable`，它已经从`CloudTableClient`中创建。这是因为 Azure Functions 负责创建和“连接池”(因为缺乏更好的描述)，所以当函数扩展时，我们不需要自己动手。我们还需要正在使用的表的名称，同样，我们不需要在函数中担心这个问题，因为我们已经有了`CloudTable`。

幸运的是，我们可以从一个`CloudTable`返回到`CloudTableClient`，同时获得表的名称。为此，我在 F#代码库中创建了一些新函数:

```
module azureTableUtils
open FSharp.Azure.Storage.Table
open Microsoft.WindowsAzure.Storage.Table

let fromTableToClientAsync (table: CloudTable) q = fromTableAsync table.ServiceClient table.Name q
let fromTableToClient (table: CloudTable) q = fromTable table.ServiceClient table.Name q

let inTableToClientAsync (table: CloudTable) o = inTableAsync table.ServiceClient table.Name o
let inTableToClient (table: CloudTable) o = inTable table.ServiceClient table.Name o

let inTableToClientAsBatch (table: CloudTable) o = inTableAsBatch table.ServiceClient table.Name o
let inTableToClientAsBatchAsync (table: CloudTable) o = inTableAsBatchAsync table.ServiceClient table.Name o 
```

从`CloudTable`你可以访问`ServiceClient`得到`CloudTableClient`，`Name`给你名字！

现在我们可以在 Azure 函数中使用它，如下所示:

```
[<FunctionName("Some_Function")>]
let someFunction ([<HttpTrigger(AuthorizationLevel.Function, "get", Route = "some-function")>] req: HttpRequest)
                 ([<Table("MyData")>] dataTable: CloudTable) =
    async {
        let! data = Query.all<MyData>
                    |> fromTableToClientAsync dataTable

        let! data
             |> Seq.map (fun (d, _) ->
                 { d with Value = "Updated" })
             |> Replace
             |> autobatch
             |> List.map (inTableToClientBatch dataTable)
             |> Async.Parallel

        return OkResult() :> IActionResult
    } |> Async.StartAsTask 
```

您可以在自己的应用程序中随意使用这些函数。你将不得不把`table`参数输入为`CloudTable`,因为 F#类型的推理不能得到它本来的样子。

该死的快乐！