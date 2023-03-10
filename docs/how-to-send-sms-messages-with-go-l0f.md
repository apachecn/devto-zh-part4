# 如何使用 Go 发送手机短信

> 原文：<https://dev.to/vonagedev/how-to-send-sms-messages-with-go-l0f>

在这篇博客文章中，我们将向您展示如何使用大约 30 行代码，使用 [nexmo-go](https://github.com/nexmo-community/nexmo-go) 客户端库通过 go 发送短信。

## 先决条件

为了跟进这篇文章，你需要在你的开发机器上安装 Golang。安装说明可以在[Golang 官方网站](https://golang.org/)上找到。

或者，如果你是新手，或者你不想经历安装过程，你可以直接在 [Golang 游乐场](https://play.golang.org/)工作。

## 使用 Nexmo API 客户端进行 Go

您的 SMS 和 Go 之旅从安装 API 客户端本身开始。您可以通过运行:
来做到这一点

```
go get github.com/nexmo-community/nexmo-go 
```

Enter fullscreen mode Exit fullscreen mode

接下来，启动编辑器并创建一个名为`main.go`的新文件。然后通过键入(或复制)以下代码来搭建 Go 应用程序的基础:

```
package main

import (
    "net/http"
    "github.com/nexmo-community/nexmo-go"
)

func main() {} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:如果保存后 import 语句中的文件消失了，不要担心，一旦您在`main()`函数中使用它们，它们就会回来。

现在是时候给这些骨骼添加一些肉，并实例化 Nexmo 客户端，这样您就可以让它真正做一些事情了。

在`main()`函数中添加以下内容:

```
// Auth
auth := nexmo.NewAuthSet()
auth.SetAPISecret("API_KEY", "API_SECRET")

// Init
Nexmoclient := nexmo.NewClient(http.DefaultClient, auth) 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了两件事。

首先，您创建一个`auth`对象，它使用一个助手函数将您的 API 密匙和秘密结合在一起，该函数将确保所有内容都被正确格式化。

> 注意:您的 API 密钥和秘密可以通过登录您的 [Nexmo 仪表板](https://dashboard.nexmo.com/sign-in)找到。如果你还没有账户，你可以[在这里](https://dashboard.nexmo.com/sign-up)注册，获得一个免费的初始信用来运行这个代码！

其次，实例化一个新的`client`，它将包含 [nexmo-go](https://github.com/nexmo-community/nexmo-go) 库提供的所有功能。您的`auth`对象被传递到这个。

有了这些，您现在可以在 Nexmo API 上执行操作，比如发送 SMS。

## 用 Go 发送短信

随着 Nexmo API 客户端的准备就绪，您的代码现在将如下所示:

```
package main

import (
    "net/http"
    "github.com/nexmo-community/nexmo-go"
)

func main() {
  // Auth 
  auth := nexmo.NewAuthSet()
  auth.SetAPISecret("API_KEY", "API_SECRET")

  // Init
  Nexmo client := nexmo.NewClient(http.DefaultClient, auth)
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使用 Go 发送 SMS，您首先需要创建一个`SendSMSRequest`对象，其中包含 SMS 到达目的地所需的所有信息。

作为一个最低限度，你应该包括号码短信应该发送到，号码，它是发送和文本显示。

可以将`SendSMSRquest`对象添加到下面的`main.go`中，在这里实例化了`client`，其格式如下:

```
smsContent := nexmo.SendSMSRequest{ From: "447700900004", To: "14155550105", Text: "This is a message sent from Go!",} 
```

Enter fullscreen mode Exit fullscreen mode

`To`号码可以是您自己的号码，但`From`号码必须是通过您的 [Nexmo 仪表板](https://dashboard.nexmo.com)购买的支持 SMS 的号码。

现在唯一要做的就是告诉我们的应用程序给发送短信。这是使用 API 客户端提供的`SendSMS`函数来完成的。

添加新的一行，代码如下:

```
smsResponse, _, err := client.SMS.SendSMS(smsContent) 
```

Enter fullscreen mode Exit fullscreen mode

最后，添加一个快速的错误检查和响应输出:

```
// If there are errors, log them out
if err != nil { log.Fatal(err) }

// All is well, print the message status returned
fmt.Println("Status:", smsResponse.Messages[0].Status) 
```

Enter fullscreen mode Exit fullscreen mode

现在，发送短信的舞台已经准备好了！到你的终端，从你正在运行的文件夹中运行:

```
go run main.go 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，你会看到`Status: 0`回到屏幕上，就在你手机短信通知的熟悉声音响起之前，标志着你成功了。

## 所有代码

供您参考，所有使用 Go 发送短信的代码如下:

```
package main

import (
    "fmt"
    "log"
    "net/http"
    "github.com/nexmo-community/nexmo-go"
)

func main() {
  // Auth 
  auth := nexmo.NewAuthSet()
  auth.SetAPISecret("API_KEY", "API_SECRET")

  // Init Nexmo 
  client := nexmo.NewClient(http.DefaultClient, auth)

  // SMS
  smsContent := nexmo.SendSMSRequest{ From: "447700900004", To: "14155550105", Text: "This is a message sent from Go!", }

  smsResponse, _, err := client.SMS.SendSMS(smsContent)

  if err != nil { log.Fatal(err) }

  fmt.Println("Status:", smsResponse.Messages[0].Status)} 
```

Enter fullscreen mode Exit fullscreen mode

## 何去何从？

您可以对上面的代码进行的下一个更改是，通过删除硬编码的 API 密钥、API 秘密和电话号码，使其更加安全。

一个好的方法是将它们移动到存储在`.env`文件中的环境变量中。

尝试使用 [godotenv](https://github.com/joho/godotenv) 包来实现这一点，并快速增强您的安全性。

## 进一步阅读

如果用 go 发送短信已经 Go，你已经知道还可以在你的应用中添加什么其他的通信元素，那么看看 [nexmo-go GitHub 库](https://github.com/nexmo-community/nexmo-go)上的例子。

在那里，你可以找到使用 Nexmo API 的许多其他方面的代码，比如打电话、接收短信和验证电话号码。

一如既往，我们渴望收到您的来信。如果您对使用 Go 和 Nexmo 有任何疑问，请考虑加入我们在 Slack 上的 [Nexmo 社区，并在那里提出您的问题。](https://developer.nexmo.com/community/slack)

帖子[如何用 Go](https://www.nexmo.com/blog/2019/08/28/how-to-send-sms-with-go-dr) 发送短信首先出现在 [Nexmo 开发者博客](https://www.nexmo.com)上。