# 尝试学习 Go - Issuer 02

> 原文：<https://dev.to/shindakun/attempting-to-learn-go-issuer-02-3nif>

# GitHub 发行者

欢迎回来！不过，如果你还没有读第一部分，你可能会想看。我们扩展了上次写的代码。增加了在我们的待办事项库中实际创建新问题并将它们添加到看板的能力。是的，最过度设计的 TODO“系统”将得到升级。说完了，让我们开始吧。

* * *

## 走吧

随着我们从标准库和一些外部包中引入一些位，我们的导入已经扩大了。一揽子计划将为我们带来相当大的负担。`oauth2`即将到来，因此我们可以使用 GitHub 个人访问令牌来授权我们的请求。

```
package main

import (
  "bytes"
  "context"
  "encoding/json"
  "fmt"
  "io/ioutil"
  "log"
  "net/http"
  "os"

  "github.com/google/go-github/v25/github"
  "github.com/shindakun/envy"
  "golang.org/x/oauth2"
) 
```

Enter fullscreen mode Exit fullscreen mode

目前，我们正在设置一些常量。我们可以将这些从代码中提取出来，并在“生产”版本中创建环境变量。对于本地测试，虽然它可能是好的。然而，这个令牌已经被设置为一个环境变量，这应该可以防止我不小心把它提交给 GitHub。尽可能在代码之外保留标记是一个好习惯。

```
const (

  // RepoOwner is the owner of the repo we want to open an issue in
  RepoOwner = "shindakun"

  // IssueRepo is the repo we want to open this new issue in.
  IssueRepo = "to"

  // ProjectColumn is the TODO column number of the project we want to add the issue to
  ProjectColumn = 5647145
)

// Token is the GitHub Personal Access Token
var Token string

// Secret is used to validate webhook payloads
var Secret string 
```

Enter fullscreen mode Exit fullscreen mode

我们的`Payload`已经设置好了，现在我们不需要来自响应的任何其他东西。我们的`status`处理程序也将保持不变。

```
// Payload of GitHub webhook
type Payload struct {
  Action string `json:"action"`
  Issue struct {
    URL string `json:"url"`
    RepositoryURL string `json:"repository_url"`
    Number int `json:"number"`
    Title string `json:"title"`
    Body string `json:"body"`
  } `json:"issue"`
  Repository struct {
    Name string `json:"name"`
  } `json:"repository"`
}

func status(res http.ResponseWriter, req *http.Request) {
  fmt.Fprintf(res, "Hello!")
} 
```

Enter fullscreen mode Exit fullscreen mode

webhook 处理程序也是这样开始的。但很快偏离。

```
func handleWebhook(res http.ResponseWriter, req *http.Request) {
  var Payload Payload
  defer req.Body.Close() 
```

Enter fullscreen mode Exit fullscreen mode

我们接受传入的请求，并将它和我们的`Secret`传递给`github.ValidatePayload()`。传入请求上的`X-Hub-Signature`带有一个签名，与我们计算的签名进行比较。如果匹配，我们就可以走了。

> 响应正文的 HMAC 十六进制摘要。如果 webhook 配置了密码，将会发送此标头。使用 sha1 散列函数和作为 HMAC 密钥的秘密来生成 HMAC 十六进制摘要。

这可以防止有人意外发现我们的端点并提交请求。当然几率很低，但是为什么要冒险呢？如果请求没有通过验证，我们只需返回并继续。

```
 p, err := github.ValidatePayload(req, []byte(Secret))
  if err != nil {
    http.Error(res, "bad request: "+err.Error(), 400)
    log.Printf("bad request: %v", err.Error())
    return
  } 
```

Enter fullscreen mode Exit fullscreen mode

`github.ValidatePayload()`返回一个有效载荷的`[]byte`，我们需要将它包装在一个“ReadCloser”中，然后我们可以将它传递给`jsonNewDecoder()`，这样我们就可以将 JSON 对象解析为我们的最终`Payload`。同样，如果出现任何问题，我们将记录错误并返回。如果一切顺利，我们将我们的`Payload`传递给`createNewIssue()`。

> *更新*正如 [@kunde21](https://dev.to/kunde21) 在评论中指出的[，这个真的应该重写使用`json.Unmarshall()`。这确实有效，但是有点难看，而且可能没有那么好的性能。](https://dev.to/kunde21/comment/c0ph)

```
 decoder := json.NewDecoder(ioutil.NopCloser(bytes.NewBuffer(p)))
  err = decoder.Decode(&Payload)
  if err != nil {
    http.Error(res, "bad request: "+err.Error(), 400)
    log.Printf("bad request: %v", err.Error())
    return
  }

  err = createNewIssue(&Payload)
  if err != nil {
    log.Printf("bad request: %v", err.Error())
    return
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先从记录我们有效负载的详细信息开始。这只是为了测试的目的，我想会被删除。

```
func createNewIssue(p *Payload) error {
  log.Printf("Creating New Issue.\n")
  log.Printf(" Name: %#v\n", p.Repository.Name)
  log.Printf(" Title: %#v\n", p.Issue.Title)
  log.Printf(" Body: %#v\n", p.Issue.Body)
  log.Printf(" URL: %#v\n", p.Issue.URL) 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们将准备好我们的 oauth2 和 GitHub 客户端。这是由 [`go-github`](https://github.com/google/go-github) 推荐的回购。

```
 ctx := context.Background()
  ts := oauth2.StaticTokenSource(
    &oauth2.Token{AccessToken: Token},
  )
  tc := oauth2.NewClient(ctx, ts)

  client := github.NewClient(tc) 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候出版我们的新一期了。我希望标题能反映出它来自哪个回购协议。

> [来自回购]记得写帖子

回购协议的主体包含最初输入的内容和返回到源回购协议的链接。然后，我们将标题和正文打包到`github.IssueRequest`中，并创建新的一期！

```
 title := fmt.Sprintf("[%s] %s", p.Repository.Name, p.Issue.Title)
  body := fmt.Sprintf("%s\n%s/%s#%d", p.Issue.Body, RepoOwner, p.Repository.Name, p.Issue.Number)

  issue := &github.IssueRequest{
    Title: &title,
    Body: &body,
  }

  ish, _, err := client.Issues.Create(ctx, RepoOwner, IssueRepo, issue)
  if err != nil {
    log.Printf("error: %v", err)
    return err
  } 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们还没有完全完成。我想确保新问题被添加到待办事项看板中。所以我们从新的发行中提取细节，提取发行 ID 号，并用`github.ProjectCardOptions`建立一个新的“卡”。

```
 id := *ish.ID
  card := &github.ProjectCardOptions{
    ContentID: id,
    ContentType: "Issue",
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们并不太关心这个调用返回的细节，所以我们只是检查错误，如果需要的话就返回。

```
 _, _, err = client.Projects.CreateProjectCard(ctx, ProjectColumn, card)
  if err != nil {
    log.Printf("error: %v", err)
    return err
  }

  return nil
} 
```

Enter fullscreen mode Exit fullscreen mode

这就把我们带到了更新的`main()`。我们添加了一些代码来获取我们的环境变量，如果没有设置，我们将会抛出一个错误。

```
func main() {
  log.Println("Issuer")
  var err error

  Token, err = envy.Get("GITHUBTOKEN")
  if err != nil || Token == "" {
    log.Printf("error: %v", err)
    os.Exit(1)
  }

  Secret, err = envy.Get("SECRET")
  if err != nil || Secret == "" {
    log.Printf("error: %v", err)
    os.Exit(1)
  }

  http.HandleFunc("/", status)
  http.HandleFunc("/webhook", handleWebhook)
  http.ListenAndServe(":3000", nil)
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 跑步

好吧，让我们运行它，并在我们的测试“从”回购中制作一个新的问题。

```
SECRET=TESTSECRET GITHUBTOKEN=1234567890 go run main.go
2019/06/15 11:23:32 Issuer
2019/06/15 11:24:42 Creating New Issue.
2019/06/15 11:24:42 Name: "from"
2019/06/15 11:24:42 Title: "asdfasdf"
2019/06/15 11:24:42 Body: "asdfasdfasdfasdfasdf"
2019/06/15 11:24:42 URL: "https://api.github.com/repos/shindakun/from/issues/13" 
```

Enter fullscreen mode Exit fullscreen mode

完美！现在，我们需要做的就是把它扔到一个盒子上，把我们的 GitHub repos webhook 设置指向正确的 URL。

* * *

## 下次

进行得相当顺利！下一次，我想我们会把它转换成可以部署在谷歌云功能上的东西！这将使它更容易部署。

欢迎提问和评论！

* * *

你可以在 GitHub 上的 repo 中找到这个和大多数其他试图学习围棋的帖子的代码。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [【新达昆】](https://github.com/shindakun) / [ atlg](https://github.com/shindakun/atlg)

### 我在 dev.to 上发布的“尝试学习围棋”帖子的来源报告

<article class="markdown-body entry-content container-lg" itemprop="text">

# 尝试学习围棋

在这里你可以找到我为尝试学习围棋而写的代码，这些代码是我在 [Dev.to](https://dev.to/shindakun) 上写的。

## 帖子索引

| 邮政 | 密码 |
| --- | --- |
| [制作下载器第 01 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-01-44gl) | - |
| [制作下载器第 02 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-02-2k7i) | - |
| [制作下载器第 03 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-03-2214) | - |
| [制作下载器第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-04-3ln9) | - |
| [制作下载器第 05 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-05-44o) | - |
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
| [发行人](https://dev.to/shindakun/attempting-to-learn-go-issuer-01-5f0k) |

…</article>

[View on GitHub](https://github.com/shindakun/atlg)

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *

| 这篇文章最初发表在 [shindakun.dev](https://shindakun.dev/?utm_source=dev&utm_medium=web&utm_campaign=devposts) 上。 |
| --- |