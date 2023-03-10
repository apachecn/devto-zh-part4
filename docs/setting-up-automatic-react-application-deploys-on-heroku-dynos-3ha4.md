# 在 Heroku Dynos 上设置自动反应应用程序部署

> 原文：<https://dev.to/jakenherman/setting-up-automatic-react-application-deploys-on-heroku-dynos-3ha4>

> 这篇文章最初是在我的网站 jakenherman.com 上分享的。

根据副标题，这是“第一部分”。在这个系列中，我们将为 MLB 的 Show 19 构建一个社区市场列表 react 应用程序，并建立一个自动管道来从 GitHub 存储库到 Heroku Dyno 构建和部署我们的 React 应用程序。在这篇文章中，我们将搭建 react 应用程序，并为 Heroku 设置自动部署，这将为我们创建列出 MLB 19 秀社区市场的所有列表所需的组件做好准备。为什么是 MLB 秀 19？因为...是*棒球*。如果你不熟悉 MLB 秀 19 -这是一个 PlayStation 4 的棒球视频游戏，它有一个社区市场，允许玩家列出棒球卡，体育场和设备，其他玩家可以购买用于他们的球队。在这个项目中，我们将只为棒球卡(即球员)建立列表，而不是体育场或设备或其他任何东西。如果您对此感兴趣，我强烈建议您扩展应用程序以满足您的需求。

我们需要做的第一件事是创建一个 GitHub 存储库，对我们的应用程序进行源代码控制，并有一个地方存储我们的代码。要做到这一点，去 GitHub.com 创建一个 GitHub 库。然后，要在本地机器上获取该存储库，可以使用 git GUI 程序，或者只使用命令行界面并运行以下命令:

```
git clone https://www.github.com/<path_to_your_project> 
```

Enter fullscreen mode Exit fullscreen mode

然后，通过运行以下命令将目录切换到 GitHub 存储库:

```
cd <your_project_name> 
```

Enter fullscreen mode Exit fullscreen mode

我们需要做的下一件事实际上是进入应用程序的肉和土豆。我们需要构建一些东西放在我们新创建的 GitHub 存储库上。为此，我们需要创建一个新的 react 应用程序。打开命令行界面并运行命令:

```
 npx create-react-app mlb-the-show-community-market-listings 
```

Enter fullscreen mode Exit fullscreen mode

这个命令将安装`react`、`react-dom`、`react-scripts`，以及其他一些你会发现有用的包。`create-react-app`是 facebook 支持的创建单页面 React 应用程序的方式，前端社区中的许多人都在使用它。与 next、razzle 和其他一些流行的服务器端呈现框架不同，CRA 在客户端呈现内容，这可能会在性能上有一些缺点，但我们不一定担心这个项目的目的。

既然您的应用程序已经创建好了，就让它到此为止吧——我们将在下一篇文章中修改代码。现在，我们将按原样签入项目。因此，通过一些 git GUI 或命令行，提交您的更改并将您的代码推送到您的 GitHub 库:

```
git add .
git commit -m 'initial commit'
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

我们希望 Heroku Dyno 在每次对 GitHub 存储库上的`master`分支进行更改时重建我们的应用程序。但是在我们设置这个之前，我们需要创建一个 Heroku 管道！假设您已经创建了一个 Heroku 应用程序，导航到 dashboard.heroku.com/apps,，然后选择“新建”,然后选择“创建新管道”。在管道名称输入字段中，键入“mlb-the-show-cm-listings”。接下来，将自己设置为管道所有者，然后在显示“连接到 GitHub”的区域，将您的 GitHub 帐户连接到这个 Heroku 管道。一旦你连接了你的 GitHub 账户，通过搜索库名选择我们在文章前面创建的 GitHub 库，然后点击“连接”按钮。一旦你的 GitHub 库被连接，点击“创建管道”按钮。

创建好管道后，您将看到一个包含三个“步骤”的页面:审核应用程序、试运行、生产。在“暂存”步骤下方的卡片中，点击“添加应用程序...”按钮，后面是紫色的“创建新应用程序...”按钮。在应用程序名称中，你必须自己想一些东西，因为 heroku 应用程序名称必须是唯一的。一旦你决定了一个名字，按下“创建应用程序”按钮，以完成应用程序的创建。

现在，在你的“暂存”区域下，你看到的卡片将包含你新创建的应用程序。在“Staging”区域点击你的应用名称，打开 Heroku 应用，然后进入“Settings”。向下滚动，直到看到标有“Buildpacks”的部分。按下紫色的“添加构建包”按钮，然后从官方支持的构建包列表中选择`nodejs`构建包，然后按下“保存更改”。

接下来，单击“部署”选项卡，离开“设置”。向下滚动，直到到达标有“自动部署”的部分。按下灰色的“启用自动部署”按钮。您将看到文本更改为

> 启用从`master`开始的自动部署

现在，我们只需要启动部署。让我们对我们的项目进行更改，并提交/推送更改，这样我们的 Heroku 部署将被触发。

打开我们在本文前面创建的 React 应用程序的`src`文件夹，在`App.js`中，更改下面的代码行:

```
<p>
  Edit <code>src/App.js</code> and save to reload.
</p> 
```

Enter fullscreen mode Exit fullscreen mode

到

```
<h1>Hello Heroku!</h1> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，通过一些 git GUI 或命令行，提交您的更改并将您的代码推送到您的 GitHub 库:

```
git add .
git commit -m 'trigger heroku deploy'
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

现在导航到 heroku 应用程序的活动页面，查看是否触发了构建:`https://dashboard.heroku.com/apps/<your_app_name>/activity`。如果构建由于类似下面的消息而失败

> Heroku 上的 Node.js 应用程序要求在目录结构的根目录下有一个“package.json”。

然后，你需要做的就是确保你的 github 存储库不包含另一个包含 react 应用的文件夹，而不是包含 react 应用本身的存储库。

另一个常见问题是拥有一个过时的 Yarn 锁文件，所以如果您仍然遇到构建失败，请转到您的命令行(在 react 应用程序目录中)，并运行以下命令:

```
yarn install
git add yarn.lock
git commit -m 'updated yarn lockfile'
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

如果没有失败，您将看到“构建正在进行中...”一段时间，然后将更改为“构建成功”。一旦“构建成功”，您的活动提要上将会创建一个新的活动，并显示“已部署`SHA_OF_YOUR_COMMIT`”。

这很好——我们已经设置了一个 Heroku dyno，以便在发生更改时自动部署 react 应用程序。唯一的问题是...它部署到哪里？离开“活动”标签，再次进入“设置”。向下滚动，直到看到“域和证书”。在“域”部分，您将会看到一行类似如下的内容:

> 您的应用程序位于

导航到该域地址将显示您的应用程序。请记住，您可以随时添加自定义域。

太棒了——现在我们已经有了一个自动部署到 Heroku dyno 的 react 应用程序，现在我们只需要 react 应用程序做一些很酷的事情！在下一篇文章中，我们将开始创建查看社区市场列表所需的 React 组件。