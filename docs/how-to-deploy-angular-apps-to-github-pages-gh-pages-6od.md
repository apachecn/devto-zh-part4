# 如何将 Angular 应用部署到 GitHub 页面(gh-pages)

> 原文：<https://dev.to/josylad/how-to-deploy-angular-apps-to-github-pages-gh-pages-6od>

使用两种不同的角度校准方法

[![](img/3d013bed5493e639d95f73c384a0b014.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a_4c2gvA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_MzyRrvtSudak_DHxjIwTg.png)

将 angular 应用程序部署到 Github 页面可能有点棘手，因为 Angular 应用程序不像任何其他使用 HTML、CSS 和 JS 构建的 web 应用程序，您可以在本地计算机上构建这些应用程序，然后简单地推送到 Github 页面。

当您使用 angular CLI 构建一个 Angular 项目时，您需要采取一些额外的步骤来让您的应用程序为部署做好准备。

我将与你分享两种将 Angular 应用程序部署到 gh-pages (Github Pages)的方法。

**P.S.** —请注意，在将您的 angular 应用程序部署到 github pages 后，它可能需要大约 10-30 秒才能真正显示出来，有时 Github pages 不会立即加载您的网站，因此请在将您的 angular 网站部署到 Github Pages 几秒钟后尝试重新加载页面，以检查它是否工作。

### 方法 1

下面是将 Angular 应用程序部署到 Github 页面的第一种方法。

#### 第一步

**创建你的 Github 库:**

如果您想在 Github 页面上部署 Angular 应用程序，首先要做的是为您的项目创建一个 repo。

在创建 repo 之后，提交您的所有更改，并将您的项目文件推送到您已经创建的存储库中。

#### 第二步

**安装角度控制页面:**

有一个工具供您使用，您可以使用它轻松地将您的 angular 应用程序部署到 gh-pages，angular-cli-pages 工具。

angular-cli-pages 工具用作 angular CLI 上的命令，用于部署目的。要安装它，让我们在终端上运行以下命令:

> $ npm i angular-cli-ghpages —保存-开发

**注意:**该命令将在您的操作系统中全局安装 angular-cli-ghpages。不用再装了，万一以后需要用。

#### 第三步

**运行构建:**

在部署 angular 应用程序之前，您需要构建用于生产的 Angular 应用程序。

这仅仅意味着将你的应用程序配置成可以在远程服务器上使用，比如 gh-pages。

通常，在创建您的应用程序时，您让它在本地服务器上运行，但不幸的是，当应用程序部署在 gh-pages 远程服务器上时，本地服务器的配置将不会为应用程序服务，这就是为什么您需要构建您的应用程序。

在您的终端上运行这个命令来构建应用程序:

```
$ ng build --prod --base-href "https://GithubUserName.github.io/GithubRepoName/" 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**确保在“Github username”中输入您的 Github 用户名，并在 URL 的“GithubRepoName”部分输入您创建的存储库名称。

#### 第四步

**部署到 gh 页面:**

构建应用程序后，现在可以使用 angular-cli-ghpages 工具将其部署到 Github 页面。

在您的终端中运行以下命令来部署应用程序:

> $ npx angular-CLI-GH pages—dir = dist/Project-name

**注意:**确保将项目名称放在上面命令中的“项目名称”位置。

您可以在文件底部的 defaultProject 下的 **angular.json 文件中找到它。项目名称不对，你的 App 就不行；因此，如果你看到任何错误，**检查 angular.json 以确认你是否使用了正确的项目名称。****

#### 第六步

**访问应用页面:**

访问您的应用程序 gh-pages 的 URL，这与您之前在我们的终端上创建的 URL 相同；[**https://GithubUserName.github.io/GithubRepoName/**](https://githubusername.github.io/GithubRepoName/)

 **您应该看到您的应用程序远程运行，这意味着它已经成功地发布在 gh-pages 上。

[![](img/80dd1244964036626520408b1793adb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QM99lVao--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/300/1%2A8qz-KpQ9537NNApkl6F0kg.png)

### 方法二

下面是将 Angular 应用程序部署到 Github 页面的第一种方法。

#### 第一步

**创建你的 Github 库:**

如果您想在 Github 页面上部署 Angular 应用程序，首先要做的是为您的项目创建一个 repo。

在创建 repo 之后，提交您的所有更改，并将您的项目文件推送到您已经创建的存储库中。

#### 第二步

**在本地机器上创建一个 gh-pages 分支:**

您需要从您计算机上的主分支创建一个 gh-pages 分支，并签出到该分支。

在您的终端中运行以下命令，创建一个 gh-pages 分支:

> $ git 分支 GH-页数
> 
> $ git 结帐 GH-页面

#### 第三步

**构建您的应用:**

签出到你的 gh-pages 分支，在这个分支中构建你的 Angular 应用程序。

在您的终端中运行以下命令来构建您的应用程序:

> $ git checkout -b gh-pages
> 
> $ git 推送源 GH-页面
> 
> $ NPM install-g angular-CLI-GH pages
> 
> $ ng build-prod-base-href https://[用户名].github.io/[repo]/
> 
> $ NGH—dir = dist/[项目名称]

**注意:**确保在上面命令中的“项目名称”处输入您的“用户名”、“回购名称”和项目名称。

您可以在文件底部的 defaultProject 下的 **angular.json 文件中找到它。项目名称不对，你的 App 就不行；因此，如果你看到任何错误，**检查 angular.json 以确认你是否使用了正确的项目名称。****

**您只需设置一次“— base-href”标志，下次进行更改并构建项目时，您只需运行:

> $ ng 构建—产品

#### 第四步

**访问应用页面:**

访问您的 Angular app gh-pages 的 URL，这与您之前在我们的终端上创建的 URL 相同；[**https://UserName.github.io/RepoName/**](https://githubusername.github.io/GithubRepoName/)

 **您应该看到您的应用程序远程运行，这意味着它已经成功地发布在 gh-pages 上。

### 结论

我希望您能够使用上述任何一种方法将 Angular 应用程序部署到 Github 页面。如果你有任何问题或建议，请在评论中提出。

在你离开之前，别忘了在你的社交媒体上分享这篇文章，也许有人会觉得有用。T3】

* * *****