# 用 tbot 在 Golang 建造和部署电报机器人。

> 原文：<https://dev.to/sixpeteunder/building-and-deploying-a-telegram-bot-in-golang-with-tbot-191h>

用 Telegram 自己的话说，Telegram 是一款基于云的移动和桌面消息应用，专注于安全性和速度。凭借其内置的“机器人”和开放的 API，它似乎是以开发者和修补者为中心从头开始构建的。

[Go 编程语言](https://golang.org)是，再一次用它的开发者自己的话来说，一种开源编程语言，使得构建简单、可靠和高效的软件变得容易。它因其速度和简单等优点而受到称赞。

#### 机器人

电报机器人非常通用，可以有从接受服务付款到家庭自动化的用例。然而，为了本教程，我们将建立一个机器人，与你一起玩石头，布，剪刀，如果你发现自己很孤独。对于这个任务，我们将使用 [tbot](https://github.com/yanzay/tbot) 库。

#### 先决条件

首先你需要安装 Go 1.12+。Go 可以从[这里](https://golang.org/dl/)下载安装。[本指南](https://golang.org/doc/install)将带你完成 Go 的安装和设置。

接下来，我们将使用 [Visual Studio 代码](https://code.visualstudio.com)。VS 代码的 Go 扩展可以在[这里](https://marketplace.visualstudio.com/items?itemName=ms-vscode.Go)找到，或者使用内置的扩展工具。你也可以使用[崇高文本 3](https://www.sublimetext.com/3) 和[戈朗建造](https://github.com/golang/sublime-build)套装。

可选地，如果你有兴趣将你的机器人部署到云中，你需要安装 Heroku CLI 和 T2 Git。

### 构建 bot

#### 设置

我将使用 Go 模块构建这个项目。因此，您需要在 GOPATH 之外的某个地方创建项目文件夹，并在其中运行 go mod init github.com//，如下所示:

```
go mod init github.com/sixpeteunder/rochambeau 
```

Enter fullscreen mode Exit fullscreen mode

这将在项目文件夹中创建 go.mod 和 go.sum 文件。设置完成后，Go 将自动获取所有依赖项。

接下来，你需要和机器人父亲聊天。发送/newbot 并按照说明创建您的 bot，之后您将获得一个 bot 令牌。我要在这里建立的机器人可以在用户名 [@rochambeau_bot](https://t.me/rochambeau_bot) 下找到(更多关于那个[在这里](https://www.wrpsa.com/why-do-people-call-rock-paper-scissors-roshambo/))。

#### 构建 bot

首先创建一个. env 文件来保存您的令牌(如果您正在使用版本控制系统，您可能希望将其从版本控制系统中排除):