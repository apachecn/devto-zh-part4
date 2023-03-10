# 不和谐音乐机器人如何设置不和谐音乐机器人(2021 年 12 月更新)

> 原文：<https://dev.to/galnir/how-to-make-a-discord-music-bot-3c0p>

安装一个音乐机器人并不容易，因为它需要安装很多不同的东西。在本指南中，我将列出所有的要求，并为您提供所需的代码。确保您遵循所有步骤:)

**我们开始吧！**

## 创建一个机器人

首先，我们需要创建机器人，进入[这里](https://discord.com/developers/applications)并确保你已经登录。点击*新应用*，给你的机器人起个名字。
转到左边的“机器人”选项卡，点击“构建机器人”选项卡下的*添加机器人*。复制令牌并保存它，因为我们稍后会用到它。
复制“一般信息”选项卡下的应用程序 ID，并保存以备后用。

现在点击“OAuth2”标签，然后点击“网址生成器”。在“范围”下，选中“机器人”和“应用程序.命令”。在“Bot 权限”下，选中“管理员”。现在复制“生成的 URL”下的 URL，并将其粘贴到您的浏览器中，以便邀请您新创建的 bot 到您的 Discord 服务器！

## 代码编辑器

代码编辑器让我们编写和修改代码。我使用和推荐的代码编辑器是 [**Visual Studio 代码**](https://code.visualstudio.com/Download) 。
你也可以使用其他伟大的编辑器，比如 [**崇高文本**](https://www.sublimetext.com/) 和 [**原子**](https://atom.io/) 。

## Node.js 和 npm

*Node.js* 是一个 JavaScript 运行时。它基本上允许我们在自己的机器上运行 JavaScript 代码。我们还使用它的软件包生态系统， **npm** ，我们将在下面讨论。
从其 [**官网**](https://nodejs.org/en/) (LTS 或最新)下载 Node.js。为了检查安装是否正确，打开你的终端(cmd 或 bash，取决于你使用的操作系统)，运行 **node -v** ，你应该会得到一个数字。如果你正在努力安装节点，看看这个[指南](https://www.geeksforgeeks.org/installation-of-node-js-on-windows/)。
nom 随 Node.js 一起安装，我们稍后将使用 npm 来安装依赖项。

## 去吧

**Git** 是一个开源的版本控制系统。它通过跟踪项目的整个历史来帮助开发人员管理项目，并使添加功能变得容易，而不用担心破坏整个代码库。我们将使用 Git 从 **GitHub** 获取我们的机器人代码。GitHub 是 Git 存储库的托管服务。你可以从这篇精彩的[文章](https://codeburst.io/git-and-github-in-a-nutshell-b0a3cc06458f)中了解更多关于 Git 和 Github 的知识。

如果你在 Windows 上，从[这里](https://git-scm.com/download/win)安装 Git。对于 macOS 用户，安装 Xcode，然后点击 **git - version** ，它会提示你安装。如果你使用基于 Linux 的操作系统，点击 **sudo dnf install git-all** 或**sudo install git-all**for debian。

## Java 13

从[这里](https://www.azul.com/downloads/?version=java-13-mts&package=jdk)下载 Java 13，确保你下载的是你操作系统的正确版本。

## 克隆资源库

转到您的终端(cmd、git bash 或 bash)，您可能会在左边看到您现在位于您的用户文件夹(C:\ Users/“username”)中。进入 *cd 桌面*进入你的桌面。然后'**饭桶克隆[https://github.com/galnir/Master-Bot](https://github.com/galnir/Master-Bot)T5 '。您正在从 GitHub 克隆(复制)存储库，因此您可以使用它的代码。**

## 获取 Lavalink

从[这里](https://github.com/Cog-Creators/Lavalink-Jars/releases)(在资产下)下载最新的 Lavalink jar 文件。将 jar 文件放在项目的根目录中(与 index.js 和 package.json 在同一个文件夹中)

## 申请. yml

在之前放置 jar 文件的同一个文件夹中创建一个名为 **application.yml** 的文件。将所有内容从[这个例子](https://github.com/freyacodes/Lavalink/blob/master/LavalinkServer/application.yml.example)复制到你的 application.yml

## 令牌

为了让一切正常运行，我们需要以下令牌:

*   机器人不和谐 ID(保存自第一部分)
*   bots 令牌(保存自第一部分)
*   Spotify 客户端 ID
*   Spotify 客户秘密

为了生成最后 2 个令牌，您需要创建一个 Spotify 帐户并登录到[开发仪表板](https://developer.spotify.com/dashboard/login)
登录后，只需创建一个应用程序并复制客户端 ID 和密码。

## Config.json

在项目的根目录下创建一个名为 **config.json** 的文件，如下所示:

```
{  "client_id":  "the-bots-discord-id",  "token":  "Your-Bot-Token",  "lava_host":  "0.0.0.0",  "lava_pass":  "youshallnotpass",  "spotify_client_id":  "get-it-from-spotify-dev",  "spotify_client_secret":  "get-it-from-spotify-dev"  } 
```

Enter fullscreen mode Exit fullscreen mode

将您生成的每个令牌放在用引号括起来的位置

## 运行 bot

在项目根目录下打开一个终端，运行 **java -jar Lavalink.jar** 。
现在在同一个文件夹中打开另一个终端实例并运行 **node index.js**

运行完这两个命令后，你应该可以使用新的斜线功能运行音乐命令了！只需在文本通道中键入 **/** ，您应该会看到命令列表！

如果您有任何疑问/问题，您可以在此处评论或在回购中提出问题。另外，如果你觉得有帮助，不要忘记在回购协议中留下一个星号:)

bot repo:[https://github . com/galnir/master-bot](https://github.com/galnir/Master-Bot)
封面图片由[马耳他](https://unsplash.com/@maltewingen):)