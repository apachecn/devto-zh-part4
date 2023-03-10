# CLI 第 2 部分:在命令行中与 Heroku 交互

> 原文：<https://dev.to/heroku/the-cli-part-2-interacting-with-heroku-in-the-command-line-4p6l>

Heroku 的特别之处在于用户能够在命令行中执行各种任务。任何用户都可以创建和扩展应用程序，管理软件包，调整设置，并检查他们的 Heroku 应用程序的健康状况，而无需加载 Heroku 仪表板。既然我们已经深入了解了 Unix 命令行的基础知识(在[第一部分](https://dev.to/heroku/the-cli-for-beginners-63f)中有所介绍)，我们可以开始使用 Heroku CLI 了。

让我们开始吧！

> 在我们深入讨论之前，请注意本文是在 macOS 笔记本电脑上编写的，如果您在 Windows 或 Linux 机器上，一些命令可能会略有不同。

### 安装 Heroku CLI 并登录

在我们做任何令人兴奋的事情之前，我们必须在命令行中安装 Heroku CLI。如果你安装了 Git 和 Homebrew，这是一个简单的任务。您需要[注册 Heroku](https://signup.heroku.com/) 才能开始:)

您可以使用 Heroku 安装程序将 Heroku CLI 添加到您的系统中。Heroku CLI 文档详细介绍了安装选项。

> 如果以上方法行不通，可以在*大部分*电脑上使用家酿

如果你没有安装自制软件，说明在自制软件网站上

在命令行中运行`brew tap heroku/brew && brew install heroku`,一切都应该准备好了。

初始化你的应用

您需要用您的代码初始化文件夹中的 git 存储库。导航到您希望在 Heroku 上托管的项目目录，并运行`git init`。

> 大多数早期开发人员的一个常见困惑点是 git 和 GitHub 之间。软件工具 git 可以在您自己的机器上本地使用，或者与许多不同的在线(通常称为“远程”)存储库一起使用。GitHub 是远程存储库服务中最大、最流行的选项。在本教程中，我们将使用 git 创建一个本地存储库，并将 Heroku 作为我们的远程存储库

### 与 Heroku 认证

一旦你安装了 CLI，你需要登录你的 Heroku 账户，然后才能充分利用这个工具。运行`heroku login`并输入您的凭证以此方式登录。接下来，导航到您希望在 Heroku 上托管的项目目录，并运行`heroku create`来创建一个新的应用程序。这样做将为你的应用程序生成一个随机的名字，或者你可以通过运行`heroku create [app-name-here]`来分配一个名字。

### 将您的应用部署到 Heroku

如果您浏览到创建应用程序时为您生成的 URL，您只会看到一个默认页面，显示您的应用程序正在构建中。下一阶段的部署只需几分钟。我们需要用一些代码填充我们的应用程序。

如果你的应用还没有被设置为 git 库，你会想要从命令行运行`git init.`如果你不熟悉 git，这是[一个可靠的指南](https://medium.com/@george.seif94/a-full-tutorial-on-how-to-use-github-88466bac7d42)，它不会太深入它的复杂性。

一旦你创建了 Heroku 应用程序，在提交将你的项目目录推送到你运行`heroku create`时建立的 URL 之后，运行`git push heroku master`。请注意，根据您的应用程序所使用的语言，必须调整一些其他配置设置才能使其工作。幸运的是，你可以在 Heroku [文档](https://devcenter.heroku.com/categories/language-support)中找到关于使用多种语言的信息。Heroku 在开发中心有七种不同语言[的初学者指南和“入门应用”。](https://devcenter.heroku.com/start)

### 在本地运行您的应用

将你的代码上传到 Heroku cloud 大概需要半分钟。将一个应用程序推送到 Heroku，仅仅因为一个配置错误就让它崩溃，这可能会让人头疼。

在这一步之前，您需要在本地安装应用程序的依赖项。有了 node app，你就可以用`npm install`做到这一点

Heroku CLI 提供了在本地运行应用程序的功能，因此您可以完全避免这个问题。运行`heroku local`启动应用程序，并在浏览器中导航至 [http://localhost:5000](http://localhost:5000) 查看网站。

### 其他 CLI 功能-重命名应用程序、查看应用程序、获取日志等

创建和部署应用程序的能力是 Heroku CLI 的一个重要方面，但它还可以做许多其他事情。如果你想重命名你的应用，在应用文件夹中运行`heroku apps:rename [newname]`，用你想更改的名字替换掉新名字。通过运行`heroku apps`，你还可以看到你创建或贡献的 Heroku 应用的完整列表。如果你遇到了问题，想要查看与你的项目相关的日志，从应用目录运行`heroku logs`。运行`heroku help`可以找到命令及其功能的完整列表。

Heroku CLI 是一个功能强大的通用工具，具有多种使用案例。一旦您对命令行有了一些基本的了解，在命令行中使用客户机就变得轻而易举了。如果您希望利用某个特定的特性，或者需要重温 CLI 的某些功能，您可以随时在此处查看 Heroku 文档。