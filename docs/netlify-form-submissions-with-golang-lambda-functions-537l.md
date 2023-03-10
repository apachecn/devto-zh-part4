# 使用 Golang Lambda 函数提交网络表单

> 原文：<https://dev.to/camilo/netlify-form-submissions-with-golang-lambda-functions-537l>

静态网站是一个很好的方式来重新捕捉网络历史中一些最好的部分。它们提供了低开销和简单的部署。然而，静态网站失去的一个东西是使用后端处理表单数据的能力。Netlify 是一个静态网站托管服务，它利用 AWS Lambda 解决了这个问题。AWS Lambda 允许您运行后端代码，而无需托管整个应用程序:您只需运行您需要的代码。

本教程将从设置一个网络表单开始。接下来，我们将创建 Lambda 函数来处理表单提交。最后，我们将在 Netlify 上部署它。我们的用例将是一个简单的联系表单，通过电子邮件通知提交者他们的联系表单已被收到。

### 表单设置

Netlify 表单是 Netlify 将自动处理的表单，它们已经非常强大了。默认情况下，Netlify 处理的表单将其数据转储到网站的 Netlify 管理区，然后可以通过管理区或 Netlify API 访问这些数据。如果您不需要立即处理表单数据，这可能是一个不错的选择！

我们的联系表单需要一些基本信息:姓名、电子邮件和消息。要使一个表单成为一个 Netlify 表单，我们所要做的就是将`data-netlify=true`添加到我们的表单元素中。

```
<form name="contact" method="POST" data-netlify="true">
  <p>
    <label>Your Name: <input type="text" name="name" /></label>   
  </p>
  <p>
    <label>Your Email: <input type="email" name="email" /></label>
  </p>
  <p>
    <label>Message: <textarea name="message"></textarea></label>
  </p>
  <p>
    <button type="submit">Send</button>
  </p>
</form> 
```

很简单，对吧？现在，这些信息将被转储到我们的网络管理区，不做任何更改，但我们希望通过电子邮件将表单发送给提交者和我们自己。

### 表格和功能

AWS Lambda 函数让我们能够在不运行服务器的情况下运行服务器端代码。Netlify 用[事件触发器](https://www.netlify.com/docs/functions/#event-triggered-functions)为某些用例包装 AWS Lambda，主要围绕部署和服务协调。但是，表单提交也有一个挂钩！这给了我们一个机会。Netlify 支持这些函数的 Javascript 或 Go；我们要用 Go。

为此，我们需要一些 Go 代码来运行，然后 Netlify 将我们的 Go 代码构建到正确命名的文件中，然后我们就可以开始了！

我们的用例很简单。我们希望从联系人表单中获取输入，并将其镜像回提交者，同时抄送给我们自己。

Netlify 上一个非常基本的 Go 函数的需求是一个设置 lambda 处理程序的主函数，以及那个处理程序函数。你可以在这里阅读我们函数的完整代码:[https://github . com/camilopayan/netlify-go-function-example/blob/master/main . go](https://github.com/camilopayan/netlify-go-function-example/blob/master/main.go)

### 获取表单数据

Netlify 将表单数据作为 JSON 对象的一部分注入请求体。如果你想更详细地了解整个 JSON 对象，那么看看[https://open-api.netlify.com/](https://open-api.netlify.com/)

然而，对于我们的用例，我们将使用 Go 的内置 JSON 解组器来将请求体转换成 Go 结构。

```
type Form struct {
    Name    string `json:"name"`
    Email   string `json:"email"`
    Message string `json:"message"`
}

type Payload struct {
    Form Form `json:"data"`
}

type Body struct {
    Payload Payload `json:"payload"`
} 
```

通过使用嵌套结构，将字段标记为 json 对象中的字段，我们可以使用 JSON。解组将表单数据转换成可用的结构！

### 获取秘密数据

如果我们要发送电子邮件或接入任何第三方服务(也许你想把你的客户信息转储到 Airtable！)那么我们需要一种安全的方法来获取这些秘密，比如 API 密钥或 SMTP 凭证。

为此，Netlify 提供了环境变量，这些变量将随您的函数一起提供。您可以在 netlify.toml 文件中设置一些变量，但这意味着构建变量(npm 版本等)。机密应该通过您的网络管理仪表板存储。你可以在网络文档中了解更多关于环境变量的信息。

要访问这些环境变量，可以使用内置的 Go `os`库。

```
port, _      := strconv.Atoi(os.Getenv("MAIL_PORT"))
host         := os.Getenv("MAIL_HOST")
senderemail  := os.Getenv("MAIL_SENDER_EMAIL")
smtppassword := os.Getenv("MAIL_PASSWORD")
smtpuser     := os.Getenv("MAIL_USERNAME") 
```

### 构建您的功能

Netlify 最酷的一点是它内置的持续部署管道。如果你有可能的话(使用最新的 Go)，我推荐你保持轻松的生活，使用 Go 模块。

首先，在本地您需要运行`go mod init`来创建您的 go.mod 文件并引入您的依赖项。把这个提交给你的 git repo。

接下来，我使用一个 Makefile，它包含一组非常简单的命令:

```
mkdir -p functions
go build -o functions/submission-created ./... 
```

这将创建 functions 目录，Netlify 将在其中查找任何函数，并构建 go 文件，将它们编译成一个名为`submission-created`的文件。您可能还记得前面的内容，即`submission-created`是受支持的事件触发函数之一。

您还应该修改您的 netlify.toml 文件，以便作为您的部署的一部分运行`make build`。

有了这些，您应该有足够的基础信息来开始编写自己的表单处理函数，而不必费力地阅读 AWS Lambda 文档。不过，Netlify 函数比这更灵活，您甚至可以部署实现完整 API 的函数，或者只是从 JS 前端实现一些 RPC 端点。