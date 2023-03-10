# 尝试学习围棋-发行人 03 -云函数围棋！

> 原文：<https://dev.to/shindakun/attempting-to-learn-go-issuer-03-cloud-function-go-27co>

# 在云中运行

欢迎回来！我们刚刚完成了 GitHub TODO issue creator 的工作。这一次，我们将按照步骤将其部署为 Google Cloud 功能。为此，我们需要修改代码。但是在我们深入研究代码本身之前，我们需要设置使用 Go 模块。

* * *

## Go Mod

首先，我们需要准备好我们的`mod`文件。您可能已经启用了模块，也可能没有。如果你这样做了，你应该可以省略掉命令的`GO111MODULE=on`部分。

```
GO111MODULE=on go mod issuer 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要确保我们已经得到了我们需要的所有模块。

```
➜ issuer GO111MODULE=on go mod tidy
go: finding github.com/google/go-github/v25/github latest
go: finding golang.org/x/oauth2 latest
go: downloading google.golang.org/appengine v1.4.0
go: extracting google.golang.org/appengine v1.4.0
go: downloading github.com/golang/protobuf v1.2.0
go: extracting github.com/golang/protobuf v1.2.0
go: downloading golang.org/x/sync v0.0.0-20190227155943-e225da77a7e6
go: extracting golang.org/x/sync v0.0.0-20190227155943-e225da77a7e6 
```

Enter fullscreen mode Exit fullscreen mode

目前看起来不错！接下来，我们想出售我们的依赖项。这将把我们在上一步中下载的模块复制到我们的代码目录中。你真的不需要做这一步，反正我是包括了。

```
GO111MODULE=on go mod vendor 
```

Enter fullscreen mode Exit fullscreen mode

我们现在应该可以看到供应商目录。

```
➜ issuer ls go.mod go.sum main.go vendor 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 改建

现在让我们对我们的代码做一些修改，让它为作为云功能部署做好准备。我们将和上次一样开始。您可能会注意到，我已经删除了查询`/status`的功能，不再需要它了。

```
package issuer

import (
  "context"
  "encoding/json"
  "fmt"
  "log"
  "net/http"
  "os"
  "strconv"

  "github.com/google/go-github/v25/github"
  "github.com/shindakun/envy"
  "golang.org/x/oauth2"
) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我已经取出了我们的常数，并替换为 will 变量。这允许我们使代码更加通用，这样其他人也可以使用它。稍后我们将介绍如何设置这些变量。注意，有些人可能不喜欢在这里使用全局变量。因为我们的函数很小，我们很清楚每一步发生了什么，所以现在可以使用它们。

当然，如果你不想让全局变量随意删除它们。您可以将它们硬编码为常量，或者加载到我们的主函数调用中。

```
// RepoOwner is the owner of the repo we want to open an issue in
var RepoOwner string

// IssueRepo is the repo we want to open this new issue in
var IssueRepo string

// ProjectColumn is the TODO column number of the project we want to add the issue to
var ProjectColumn int64

// Token is the GitHub Personal Access Token
var Token string

// Secret is used to validate payloads
var Secret string

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
```

Enter fullscreen mode Exit fullscreen mode

我还把`handleWebhook()`改成了`HandleWebhook()`。导出函数使我们可以称之为云函数。

```
func HandleWebhook(res http.ResponseWriter, req *http.Request) {
  var Payload Payload
  defer req.Body.Close()

  p, err := github.ValidatePayload(req, []byte(Secret))
  if err != nil {
    http.Error(res, "bad request: "+err.Error(), 400)
    log.Printf("bad request: %v", err.Error())
    return
  } 
```

Enter fullscreen mode Exit fullscreen mode

正如@kunde21 上周指出的，我们在这里使用`json.Unmarshal()`会更好。这也允许我们删除`bytes`和`io/ioutil`的导入。

```
err = json.Unmarshal(p, &Payload)
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

func createNewIssue(p *Payload) error {
  log.Printf("Creating New Issue.\n")
  log.Printf(" Name: %#v\n", p.Repository.Name)
  log.Printf(" Title: %#v\n", p.Issue.Title)
  log.Printf(" Body: %#v\n", p.Issue.Body)
  log.Printf(" URL: %#v\n", p.Issue.URL)

  ctx := context.Background()
  ts := oauth2.StaticTokenSource(
    &oauth2.Token{AccessToken: Token},
  )
  tc := oauth2.NewClient(ctx, ts)

  client := github.NewClient(tc)

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

  id := *ish.ID
  card := &github.ProjectCardOptions{
    ContentID: id,
    ContentType: "Issue",
  }

  _, _, err = client.Projects.CreateProjectCard(ctx, ProjectColumn, card)
  if err != nil {
    log.Printf("error: %v", err)
    return err
  }

  return nil
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们来看一个最大的变化。既然云函数要调用`HandleWebhook()`，我们就不再需要我们的`main()`。但这也带来了一个问题，我们有一些想要使用的环境变量。我们可以在`HandleWebhook()`调用中加载它们，使用[围棋的`init()`](https://golang.org/doc/effective_go.html#init) 更合适。

`init()`在 main(或者我们的处理程序)之前运行，这允许我们像平常一样加载变量。

```
func init() {
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

  RepoOwner, err = envy.Get("REPOOWNER")
  if err != nil || RepoOwner == "" {
    log.Printf("error: %v", err)
    os.Exit(1)
  }

  IssueRepo, err = envy.Get("ISSUEREPO")
  if err != nil || IssueRepo == "" {
    log.Printf("error: %v", err)
    os.Exit(1)
  } 
```

Enter fullscreen mode Exit fullscreen mode

`ProjectColumn`需要更多的设置，因为我从未扩展过`envy`来返回`int` s。所以我们需要转换成`int64`，然后才能使用列数字 ID 在看板上创建我们的待办事项卡。

```
n, err := envy.Get("PROJECTCOLUMN")
  if err != nil || n == "" {
    log.Printf("error: %v", err)
    os.Exit(1)
  }
    ProjectColumn, err = strconv.ParseInt(n, 10, 64)
      if err != nil || ProjectColumn == 0 {
    log.Printf("error: %v", err)
    os.Exit(1)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 去云端

我将假设您已经安装了 Google Cloud 命令行工具，并设置了一个项目。如果你没有，谷歌有一些非常好的教程。查看[https://cloud.google.com/functions/docs/quickstart](https://cloud.google.com/functions/docs/quickstart)了解更多

> 注意:您不希望在生产中像这样部署秘密和令牌。你可能会想使用[云 KMS](https://cloud.google.com/kms/docs/secret-management) 或[运行时配置器](https://cloud.google.com/deployment-manager/runtime-configurator/)。我活得很危险。

```
➜ issuer gcloud functions deploy issuer --entry-point HandleWebhook --runtime go111 --trigger-http --memory=128MB --set-env-vars SECRET=secret,GITHUBTOKEN=token,REPOOWNER=shindakun,ISSUEREPO=to,PROJECTCOLUMN=5647145

Deploying function (may take a while - up to 2 minutes)...done.
availableMemoryMb: 128
entryPoint: HandleWebhook
environmentVariables:
  GITHUBTOKEN: token
  ISSUEREPO: to
  PROJECTCOLUMN: '5647145'
  REPOOWNER: shindakun
  SECRET: secret
httpsTrigger:
  url: https://us-east1-golang-slackbot.cloudfunctions.net/issuer
labels:
  deployment-tool: cli-gcloud
name: projects/golang-slackbot/locations/us-east1/functions/issuer
runtime: go111
serviceAccountEmail: golang-slackbot@appspot.gserviceaccount.com
sourceUploadUrl: https://storage.googleapis.com/gcf-upload-us-central1-eeb5af0e-fd09-4fe7-30851592ebba/bcc11c6f-55fc-4d73-864a-6a89813206a6.zip?GoogleAccessId=service-84452958925@gcf-gserviceaccount.com&Expires=1561244032&Signature=QX%2BKy5j6YTA6%D%3D
status: ACTIVE
timeout: 60s
updateTime: '2019-06-22T22:24:37Z'
versionId: '1' 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 下次

我们做到了！我们现在生活和更新任何来源回购使用我们的触发网址。这就完成了我们发行人的这一阶段。新问题将出现在我们的目标回购和看板的待办事项部分！

下次我们应该处理什么？我得看一下看板，看看是否有什么想法突然出现在我眼前。在此之前，如果您发现了需要重构的地方，请随时告诉我。

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
| [发行人](https://dev.to/shindakun/attempting-to-learn-go-issuer-01-5f0k) |

…</article>

[View on GitHub](https://github.com/shindakun/atlg)

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *