# 尝试学习 Go - Issuer 01

> 原文：<https://dev.to/shindakun/attempting-to-learn-go-issuer-01-5f0k>

# GitHub 发行者

如果你没有碰巧[看过上一篇文章](https://shindakun.dev/posts/working-on-a-new-flow/)，我会给你一个简单的概述。我正在尝试一个新的系统来处理我的项目和待办事项。

问题是当前的设置非常手动。我想做的每一件“事情”，或者项目，都有自己的仓库，有自己的自动化看板。我在回购中打开一个问题，将其分配给项目委员会，然后“嘭”的一声，新任务。但是，我需要手动将该问题添加到我的主“待办事项”回购中。

我们不能这样！这使得系统比它应该做的工作更多。这意味着我们不会使用它。

但是，我们有解决办法！GitHub 库可以启用 webhooks。因此，在每一期杂志上，我们的 webhook 都会收到一个包含该期杂志详细信息的 POST 请求。这就是`Issuer`的用武之地。它将驻留在服务器上，等待传入的 POST 事件。一旦有问题，它将使用 GitHub API 在 TODO repo 中创建一个问题。

* * *

## 走吧

好吧！让我们直入主题，看看我们在处理什么。如果你阅读了`Uploader`系列的第二或第三篇文章，那么部分基本代码看起来会很相似。我们对 Go web 服务器使用非常标准的方法。

```
package  main  import  (  "encoding/json"  "fmt"  "log"  "net/http"  ) 
```

与我们实际需要的相比，GitHub 问题的有效载荷是“巨大的”。因此，我们的`Payload`结构将只包含创建新问题所需的内容。

```
type  Payload  struct  {  Action  string  `json:"action"`  Issue  struct  {  URL  string  `json:"url"`  RepositoryURL  string  `json:"repository_url"`  Number  int  `json:"number"`  Title  string  `json:"title"`  Body  string  `json:"body"`  }  `json:"issue"`  Repository  struct  {  Name  string  `json:"name"`  }  `json:"repository"`  } 
```

你好！这是不必要的，但我喜欢有它作为一个小的健康检查。在更健壮的应用程序中，您可能希望从服务器返回某种度量。然后，如果我愿意，我可以将 Uptime Robot 指向`/`来提醒它是否停机。

```
func  status(res  http.ResponseWriter,  req  *http.Request)  {  fmt.Fprintf(res,  "Hello!")  } 
```

我将介绍如何在回购中启用 webhooks，以及稍后如何进行测试。现在，让我们仔细看看我们的 webhook 处理函数。我们从设置我们的`Payload`变量和`defer`关闭请求体开始。然后我们使用`json.NewDecoder()`来准备和解码我们的 JSON 有效载荷。如果`decode.Decoder()`返回一个错误，我们将首先返回一个`400`给原始服务器。然后，我们将错误记录到控制台，然后从函数返回。

```
func  handleWebhook(res  http.ResponseWriter,  req  *http.Request)  {  var  Payload  Payload  defer  req.Body.Close()  decoder  :=  json.NewDecoder(req.Body)  err  :=  decoder.Decode(&Payload)  if  err  !=  nil  {  http.Error(res,  "bad request: "+err.Error(),  400)  log.Printf("bad request: %v",  err.Error())  return  } 
```

假设一切顺利，我们将 webhook 的详细信息打印到控制台中。

```
 log.Printf("%#v\n",  Payload.Repository.Name)  log.Printf("%#v\n",  Payload.Issue.URL)  log.Printf("%#v\n",  Payload.Issue.Title)  log.Printf("%#v\n",  Payload.Issue.Body)  log.Printf("%#v\n",  Payload.Issue.Number)  log.Printf("%#v\n",  Payload.Issue.RepositoryURL)  } 
```

对于这个基本的例子，我们的 main 由两个处理程序和`http.ListenAndServe()`组成。我们可能希望在某个时候扩展它，以允许使用替代的 HTTP 客户端。为了保持代码简单，我们暂时不写它。

```
func  main()  {  log.Println("Issuer")  http.HandleFunc("/",  status)  http.HandleFunc("/webhook",  handleWebhook)  http.ListenAndServe(":3000",  nil)  } 
```

## 下次

下次见面时，我们将增加根据收到的有效载荷创建问题的能力。如果进展顺利，我们甚至会添加代码，将问题添加到 TODO 项目中。🎉

* * *

你可以在 GitHub 上的 repo 中找到这个和大多数其他试图学习围棋的帖子的代码。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [【新达昆】](https://github.com/shindakun) / [ atlg](https://github.com/shindakun/atlg)

### 我在 dev.to 上发布的“尝试学习围棋”帖子的来源报告

<article class="markdown-body entry-content p-5" itemprop="text">

# 尝试学习围棋

在这里你可以找到我为尝试学习围棋而写的代码，这些代码是我在 [Dev.to](https://dev.to/shindakun) 上写的。

## 帖子索引

| 邮政 | 密码 |
| --- | --- |
| [制作下载器第 01 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-01-44gl) | - |
| [制作下载器第 02 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-02-2k7i) | - |
| [制作下载器第 03 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-03-2214) | - |
| [制作下载器第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-04-3ln9) | - |
| [建造下载器第 05 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-05-44o) | - |
| [使用 REST API](https://dev.to/shindakun/attempting-to-learn-go---consuming-a-rest-api-5c7g) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-01/main.go) |
| [继续休息冒险](https://dev.to/shindakun/attempting-to-learn-go---continuing-rest-adventures-2l4l) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-02/main.go) |
| [现在发送休息请求](https://dev.to/shindakun/attempting-to-learn-go---now-sending-rest-requests-akp) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-03/main.go) |
| [REST API 和模板上的位](https://dev.to/shindakun/attempting-to-learn-go---rest-api-and-a-bit-on-templates-4kca) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-04/main.go) |
| [再次通过 API 发送电子邮件](https://dev.to/shindakun/attempting-to-learn-go---sending-email-via-api-again-2e4e) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-05/main.go) |
| [让我们模块化吧！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular-390i) | [src](https://github.com/shindakun/mailgunner) |
| [让我们再一次模块化！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular---again-10cd) | [src](https://github.com/shindakun/mailgunner) |
| [构建开发日志第 1 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-01-1c3m) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-01/main.go) |
| [构建开发日志第 2 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-02-179c) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-02/main.go) |
| [构建开发日志第 3 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-03-7lk) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-03/main.go) |
| [构建开发日志第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-04-2bok) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-04/main.go) |
| [构建开发日志第 5 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-05-4mo1) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-05/main.go) |
| [按扩展名 01 列出文件](https://dev.to/shindakun/attempting-to-learn-go---listing-files-by-extension-1n10) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [按扩展名 02 列出文件](https://dev.to/shindakun/attempting-to-learn-go---sorting-and-moving-files-by-extension-227j) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [开发至 API 01](https://dev.to/shindakun/interacting-with-the-devto-article-api-4g34) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devtoapi-01/main.go) |
| [开发至 API 02](https://dev.to/shindakun/interacting-with-the-devto-article-api---again-sort-of-2o8g) | 参见上面的代码 |

</article>

[View on GitHub](https://github.com/shindakun/atlg)

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *

| 这篇文章最初发表在 [shindakun.dev](https://shindakun.dev/?utm_source=dev&utm_medium=web&utm_campaign=devposts) 上。 |
| --- |