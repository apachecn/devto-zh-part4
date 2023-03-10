# 使用 Golang 作为脚本语言

> 原文：<https://dev.to/eminetto/using-golang-as-a-scripting-language-jl2>

在我们开发 [Codenation](https://codenation.dev) 期间做出的技术决策中，正确的一个是选择 Go 作为主要语言。

由于这一选择，以及采用了[干净的架构](https://eltonminetto.net/en/post/2018-03-05-clean-architecture-using-go/)，我们通过在各种任务中使用相同的语言提高了工作效率，例如:

*   我们的 API 服务器
*   λ函数
*   [迁移](https://eltonminetto.net/en/post/2019-01-23-migracao-de-dados-com-go-e-mongodb/)
*   我们的 CLI 运行在客户端机器上
*   Slack 聊天机器人自动完成内部任务
*   这篇文章的主题是:任务自动化脚本

快速浏览一下我们的知识库，您可以看到我们是如何将该语言用于所有这些目的的:

[![go_codenation](img/b72f0cd9f685340634f4320166f90e6b.png)](https://eltonminetto.nimg/posts/go_codenation.png)

在 *cmd* 目录中，你可以看到我们将 Go 作为脚本语言的一些用法。其中一些作为团队开发工作流的一部分运行，例如 *trello-github* 自动完成这两个工具之间的集成任务。其他的通过 *crontab* 按预定任务运行，比如 *load-bi* 。还有一些作为我们的 [CI/CD](https://eltonminetto.net/en/post/2018-08-01-monorepo-drone/) 服务器工作流的一部分运行，比如*迁移*。

在这个场景中，与 shell 脚本相比，Go 的优势包括:

*   它是一种简单而强大的语言。我们可以使用类似`go routines`的特性来创建复杂的脚本
*   感谢干净的架构，我们可以重用项目其余部分使用的相同业务层
*   可以生成可以在任何开发人员机器或服务器上运行的二进制文件。不依赖于执行自动化任务会使它变得容易得多
*   我们可以通过运行`go run`来使用它，而不需要经过编译过程。这里需要有 Go 可执行文件，但是安装很简单，我们也可以自动完成。

## 有用的包

以下是我们用来创建这些小应用程序的软件包列表:

[**github.com/fatih/color**](https://github.com/fatih/color)

 **颜色允许您使用彩色输出，这增加了可用性。

[![cli_go](img/581c7f1f72502743f2881146cb267029.png)](https://eltonminetto.nimg/posts/cli_go.png)

[**github.com/schollz/progressbar**](https://github.com/schollz/progressbar)

 **在上图中，你可以看到这个包在运行。这个包使得创建进度条变得容易，在需要时间执行的过程中很有用。

[**github.com/jimlawless/whereami**](//github.com/jimlawless/whereami)

 **这个包对于生成错误消息很有用，因为它捕获了文件的名称、行、函数等。例如:

```
File: whereami_example1.go  Function: main.main Line: 15 
```

Enter fullscreen mode Exit fullscreen mode

[**github.com/spf13/cobra**](//github.com/spf13/cobra)

 **Cobra 可能是在 Go 中开发命令行应用程序最广泛使用的包。根据文档，它被用于 Kubernetes、Hugo、Docker 等大型项目。有了它，你可以创建带有输入处理、选项和文档的专业应用程序。我们在 CLI 中使用它，如下例所示:

[![cobra](img/b77456185f31f089e487099c78c3b254.png)](https://eltonminetto.nimg/posts/cobra.png)

还有其他一些包和库可以帮助您开发命令行应用程序，以自动化开发工作流中的各种任务，并为其他团队服务。查看 [Awesome Go](https://github.com/avelino/awesome-go#command-line) 项目，找到几个有趣的选项。

我希望这些建议有助于在你的项目中激发语言的新用途。********