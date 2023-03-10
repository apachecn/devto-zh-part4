# 穿过 Gitpod

> 原文：<https://dev.to/techwatching/coming-across-gitpod-46n7>

有一天，当我在寻找一种自动化开发环境设置的方法时，我遇到了 [Gitpod](https://www.gitpod.io/) 。这并不是我真正想要的，但是我发现了一个非常棒的开源项目工具。

Gitpod 是一个在线服务，旨在为 GitHub 项目在浏览器中提供现成的代码开发环境。

让我们看看几个月前我参与的一个项目:Voxxrin 配套移动应用。首先，让我们转到该项目的 GitHub 页面，在页面的 URL 前面加上`gitpod.io/#`前缀，得到如下 URL:[http://git pod . io/# https://GitHub . com/voxrin/voxrin-companion-ui](http://gitpod.io/#https://github.com/voxxrin/voxxrin-companion-ui)。

由于我已经登录到我创建的 Gitpod 帐户(对开源开发者免费，配额为 100 小时/月)，Gitpod 将开始为我创建一个开发环境。这意味着用我需要编码的所有东西拉一个 docker 映像(对于特定的需求，你也可以指定一个定制的 Docker 映像来使用)并用项目的代码为我初始化一个工作区。

不到 30 秒，我们到了: [![](img/72c6c8097d3ffca8418224f5c5947246.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IdsdCMfE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://techwatching.dev/posimg/gitpod_voxxrin_1.png)

我现在有一个完整的开发环境运行在云中，我的项目代码已经克隆。如果你对这个编辑器很熟悉，那是因为[忒伊亚](https://www.theia-ide.org/)(IDE 的名字)非常类似于 [VS 代码，但是在云中](http://typefox.io/theia-vs-code-in-the-cloud)(它重用了 VS 代码的许多部分)。这很棒，因为这意味着我可以使用我已经熟悉的 IDE，甚至可以使用 vs 代码扩展(前提是您有。vsix 文件来安装它们)。

让我们运行我的项目，这是一个离子应用程序。Node 已经安装好了，所以我只需要在编辑器中打开的终端中输入`npm install`然后输入`npm run-script serve`，就好像我正在处理我的本地 VS 代码一样。我的应用程序被配置为在端口`8100`上启动，所以 Gitpod 会建议我暴露这个端口，以便我能够在浏览器中查看我的应用程序。

[![](img/4718e8aa7c383259b1d31fcb2bfb3987.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--eAh1foN9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://techwatching.dev/posimg/gitpod_voxxrin_2.png) [ ![](img/6e6ce231d6764a57d24878a4f7d254e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h75-WC4Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://techwatching.dev/posimg/gitpod_voxxrin_3.png)

我现在可以在电脑浏览器中打开在 Gitpod 工作区中运行的应用程序。我也可以直接在 Gitpod 里预览。

[![](img/970491fb4e015bbc62f5a5895ca16a47.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Z2Vlu5QN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://techwatching.dev/posimg/gitpod_voxxrin_5.png) [ ![](img/4df26f18683b68507e3166916ee84f63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2dIbfwac--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://techwatching.dev/posimg/gitpod_voxxrin_4.png)

如果我有一些修改要做，我可以直接把它们从这里推到我的 GitHub 库，因为我的 Gitpod 帐户与我的 GitHub 帐户相关联。

完成后，我可以停止工作区，就这样。

[![](img/2d74322e387cad193097a1162c308a26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U9Gx_FX5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://techwatching.dev/posimg/gitpod_voxxrin_7.png)

我为我的项目创建了工作空间，但是很多东西可以通过在你的项目库中有一个 [`gitpod.yml`](https://www.gitpod.io/docs/41_config_gitpod_file/) 文件来配置

有了 Gitpod，你只需要一个浏览器和一个互联网连接就可以在任何地方处理你的 GitHub 项目。当你在多个开源项目上工作时，好处是它允许你很容易地从一个切换到另一个。我想知道微软在 Build 2019 上宣布的基于浏览器的网络伴侣[**Visual Studio Online**](https://devblogs.microsoft.com/visualstudio/intelligent-productivity-and-collaboration-from-anywhere/)是否会提供相同的功能，以及它与 Gitpod 有何不同。

这只是 Gitpod 的一个简单介绍，但是您可以用它做更多的事情:

*   在环境启动时运行脚本(例如 an `npm install`)
*   不断自动构建您的存储库
*   安装数据库
*   将其用于私人项目(不是免费的，有多种价格可供选择)
*   与其他用户共享环境
*   ...

但最好的是你试着自己判断。