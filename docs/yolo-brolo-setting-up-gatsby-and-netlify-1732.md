# 约洛·布罗洛——撮合盖茨比和奈特丽菲

> 原文：<https://dev.to/thisdotmedia/yolo-brolo-setting-up-gatsby-and-netlify-1732>

最近，每个开发人员都在谈论 Gatsby、static site generators 和 Netlify，所以我们决定亲自尝试这些技术，以便了解和了解它们的易用性。

Gatsby 是一个基于 React 的静态网站生成器，用于快速构建速度极快的静态网站。这是由 Gatsby CLI 实现的，它是一个真正的工具，可以让我们立刻搭建一个静态站点。

Netlify 是我们用来托管静态站点的服务，我们将与 Gatsby 一起构建这个站点。Netlify 可以与 GitHub、GitLab 和 BitBucket 存储库集成，这意味着我们可以轻松地插入任何平台上的任何存储库，并从生产分支(通常是主分支)部署到 Netlify 内容交付网络(CDN)。

**建立盖茨比项目**

要设置 Gatsby，我们需要在我们的机器上安装/更新节点和节点包管理器(npm ),然后安装 Gatsby CLI 工具，以便能够构建新的 Gatsby 项目。Gatsby CLI 工具的功能并不仅限于从头构建一个新的 Gatsby 应用程序，CLI 工具还为 Gatsby 开发人员提供了一些实用命令，用于启动和运行服务器以及为 Gatsby 站点构建静态资产。要从我们的终端(命令行)安装 Gatsby CLI，我们可以运行以下命令:

`npm install -g gatsby-cli`

这将“全局”安装 Gatsby，并使直接从终端使用 Gatsby 命令成为可能。要启动一个新的 Gatsby 项目，我们将运行以下命令:

`gatsby new gatsby-netlify`

该命令创建一个名为 **gatsby-netlify** 的新项目目录(这是项目名称，但是您可以随意命名)，其中包含我们项目所需的所有文件。Gatsby new 命令需要一个包管理器来安装我们搭建的 Gatsby 项目的包和依赖项，因此在命令执行过程中，会出现一个提示，要求我们选择一个包管理器来安装所需的包。默认情况下，我们将看到 **yarn** 和 **npm** ，但是对于本教程，我们将坚持使用 yarn，因此您应该明确选择 yarn(如果您的机器上没有安装 yarn，请确保安装它，或者您也可以选择 npm，但是在比较您运行的命令和我们的命令时，输出会有轻微的差异)。

选择 yarn 会自动运行`yarn install`命令，该命令会继续安装我们的 Gatsby 项目所需的所有包和包依赖项，在包安装完成后，yarn 最终会构建已安装的包。

**推送至 GITHUB**

我们的 Gatsby 项目现在已经准备好了，但我们需要推进 GitHub，因为它与 Netlify 惊人地集成，并且需要部署到 Netlify CDN，还有最后一个原因是帮助我们对项目进行版本化。接下来的 git 命令是基本的 Git 命令，您现在只需要安装 Git 就可以完成了。如果您是 Git 和 GitHub 的新手，互联网上有大量关于 Git 和 GitHub 的学习资料，请务必查看这些资料，以更好地理解这些命令的实际作用，但现在您可以跟着做(一些命令返回有意义的输出，可以让您了解它们的作用，因此建议阅读它们)。

运行以下命令将使我们能够将 gatsby-netlify 目录设置为 git 存储库，并成功推送至 GitHub:

`git init`

git init 命令将 gatsby-netlify 目录初始化为 Git repo 和隐藏的**。git** 文件夹被添加到目录中。

`git add –all`

**git add–all**命令将目录中的文件暂存到暂存区，这使得 git 可以跟踪它们以进行提交。

`git commit -m "initial commit"`

`git commit -m "initial commit"`命令用于获取项目目录当前状态的快照,“初始提交”消息被称为提交消息，它解释了在哪里做了什么更改以及为什么做了这些更改。

我们在 https://github.com 登录 GitHub 账户，创建一个名为 **netlify-demo** 的资源库，将其设置为公共资源库，并在没有**的情况下初始化它。gitignore** 文件或者 **README.md** 文件。成功创建存储库之后，我们应该会看到关于如何从终端(命令行)设置存储库的进一步说明。

我们已经运行了大多数命令，所以我们只需复制命令`git remote add origin git@github.com:USERNAME/netlify-demo.git`(命令中的用户名应该替换为您的 GitHub 帐户用户名)并粘贴到终端中，然后按回车键，就像这样:

`git remote add origin git@github.com:OPARA-PROSPER/netlify-demo.git`

`git remote add origin git@github.com:USERNAME/netlify-demo.git`命令用于将远程存储库添加到没有远程副本的本地 git 存储库中。

现在一切都准备好了，我们可以通过运行以下命令将项目文件推送到 GitHub:

`git push -u origin master`

这个命令将本地存储库的主分支的内容推送到添加到项目中的远程存储库的主分支。运行这个命令会提示我们输入 SSH 密码(如果您使用的是 HTTPS，情况会有所不同)，我们输入密码，一切都会顺利进行。

现在，如果我们检查 GitHub 上的存储库， **gatsby-netlify** 项目文件现在应该在 netlify 演示存储库中。

**在网络上部署**

Netlify 为部署 web 项目提供了两个界面，包括 web 界面和命令行界面。对于本教程，我们将主要使用 web 界面，因为它非常容易使用和管理。

首先，我们登录现有的 Netlify 帐户(如果您还没有帐户，可能需要设置一个)，找到项目仪表板并设置访问我们的帐户仪表板(通常使用您的用户名和团队关键字命名，例如 opara prosper 的团队)。

接下来，我们从 Git 按钮中找到**新站点，这使得从 GitHub、Bitbucket 或 GitLab 平台中选择一个存储库进行连续部署成为可能。因为我们已经有了位于 GitHub 上的 Gatsby 站点，所以我们选择 GitHub。需要注意的是，如果您是 Netlify 的新手，您需要给予 Netlify 对您的 GitHub 帐户(只读)和 GitHub 库的适当访问权限，以使 Netlify 与您的帐户集成成为可能(如果您之前已经给予 Netlify 所需的权限，那么您就不必再为这些细节而烦恼)。还要确保给 Netlify 访问 **netlify-demo** 库的权限。**

在与 GitHub 成功集成之后，接下来是设置构建选项。在这里，我们确保使用适当的团队名称和细节设置项目，接下来我们设置**分支以将**部署到**主分支**(根据设计，主分支是生产就绪分支)，因为 Netlify 使用连续部署模型(这意味着 Netlify 为每个新的 push 或 commit 运行构建命令到部署分支)，设置部署分支可以防止 Netlify 在所选部署分支(通常是主分支)之外的分支中运行构建命令。还要注意，默认情况下 Netflify 将构建命令设置为 **gatsby build** ，这是可以的，我们保持不变(还要注意，如果我们使用除 gatsby 之外的另一个静态站点生成器，例如 Jekyll，这在将来会有所不同)。最后，Netlify 将**发布目录**设置为 **build/** ，这一点我们也没有改动。

点击 **Deploy site** 按钮应该会启动部署过程，这将需要一段时间，具体取决于网络延迟。从团队的仪表板上，我们看到一个**站点部署正在进行**的通知，在它的正上方，我们还注意到一个 Netlify 自动生成的部署名称，用于当前部署到 Netlify CDN 的项目。成功部署后，我们应该会看到一个**部署日志**按钮，该按钮用于显示所有命令的 CLI 输出，以及 Netlify 为部署项目而运行的命令的成功报告。

最后，单击 **preview deploy** 命令将打开一个新的浏览器选项卡，该选项卡路由到 Netlify 上新部署的 Gatsby 项目。你好。我们刚刚部署了 Gatsby 应用程序！

**部署预览**

部署预览是 Netlify 的一项内置功能，它使 Netlify 能够自动构建和部署对 GitHub 存储库发出的每个 pull 请求，这些请求与 Netlify 同步，目的是**预览**或检查 pull 请求中提出的更改，虽然该功能会自动关闭，但也可以在**站点设置**中关闭。

为了检查部署预览功能，我们将通过编辑 **src/pages/index.js** 文件对 GitHub web 资源库进行更改，将“hello people”更改为“hello everyone”。接下来，我们保存更改，检查**为这个提交创建一个新的分支，并启动一个拉请求**单选按钮，点击**提议更改**按钮，最后点击**创建拉请求**按钮，为我们刚刚做出的更改打开一个拉请求。

创建一个 pull 请求会使 Netlify 自动对 pull 请求运行 build 命令以进行部署预览，并且还会对 pull 请求进行一些与构建同步的检查。导航回我们的 Netlify 站点仪表板，我们会注意到部署预览部分现在有一个**部署预览#1** 超链接，单击它会将我们带到一个专用仪表板，用于在浏览器上预览已部署的拉请求、发布部署、查看部署摘要以及包含有关构建命令、成功消息和错误消息的部署日志。

值得注意的是，部署预览版并不能取代本地主机上的本地测试，Netlify 并没有剥夺开发人员的这一权利，在部署到 Netlify 之前，仍然完全有可能在本地测试更改。

**使用配置文件集成网络生活**

部署到 Netlify CDN 不仅可以使用 web 界面，部署到 Netlify 也可以使用一个通常名为 **netlify.toml** 的 **TOML** ( **T** om 的 **O** bvious，**M**minimal**L**anguage)配置文件来实现。

使用 TOML 配置文件设置 netlify 的主要目的是:

1.  为使用您的源代码的其他人省去使用 web 界面重新配置他们自己的部署环境的压力。

2.  使用 Git 这样的版本控制系统时，跟踪配置设置的变化。

首先，我们在 netlify-demo 存储库的根目录下创建一个 netlify.toml 文件，然后将下面几行代码添加到该文件中:

```
[build]
  command = "yarn build"
  publish = "dist/" 
```

打开根目录下的 package.json 文件，找到**脚本**键，编辑/添加以下几行:

```
"scripts" {
   "build": "gatsby build",
   "postbuild": "mv public dist",
   ...
} 
```

接下来，我们将当前对存储库所做的更改存储在本地计算机上，然后从 GitHub 上远程提取我们所做的更改，最后将存储的内容应用到项目目录的当前状态。现在我们可以运行`yarn build`命令来构建项目，并获取 Netlify 被设置为从其部署的 dist 文件夹。

成功运行 build 命令后，我们确保将新创建的 dist 文件夹添加到**中。git ignore“public”正下方 Gatsby files 注释下的**文件，通过运行下面的 git 命令创建并签出一个新分支，我们称之为**gerry/toml**:

`git checkout -b "chore/toml"`

接下来，我们通过运行`git add –all`命令来暂存更改，通过运行`git commit -m "test if toml works"`来提交更改，通过运行以下命令来为我们创建的分支设置上游分支，以避免出现致命的 git 错误消息:

`git push –set-upstream origin chore/toml`

我们现在可以在 GitHub web 界面上检查存储库，在这里我们点击**比较&创建拉请求**按钮，为我们刚刚推送的变更创建一个拉请求。Netlify 现在获取更改，并开始为 pull 请求运行 build 命令(记住这个特性是由 deploy preview 特性启用的)。最后，我们将拉请求合并到我们的生产分支(主分支)。

导航回 Netlify web 界面，您会注意到仪表板的**生产部署**部分的变化。

**总结**

关于 Netlify 和 Gatsby，肯定还有更多内容没有在本教程中介绍，因为我们的重点更多的是快速设置这些技术以开始与它们一起工作，还有其他有趣的东西，如 Netlify CMS、Netlify CLI。我们的 Gatsby 网站内容很少，我们可以继续添加真正的内容，如何安装 Gatsby 插件，以及做类似 markdown 页面这样的事情都被遗漏了。在未来的 yolo brolo 系列中，我们肯定会更多地介绍盖茨比和 Netlify。

感谢您加入我们并一起学习。

这篇文章是由 Opara Prosper 写的。

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。