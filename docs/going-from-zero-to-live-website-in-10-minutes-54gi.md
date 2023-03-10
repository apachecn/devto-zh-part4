# 在大约 10 分钟内从零到网站上线

> 原文：<https://dev.to/rixcy/going-from-zero-to-live-website-in-10-minutes-54gi>

我最近帮助几个同事建立并运行了一个网站，他们之前没有开发/部署程序的经验，使用了与我个人作品集相同的堆栈，所以我想我应该写一份关于如何在大约 10 分钟内从零到活网站的快速指南

在本教程中，我们将使用 [Gatsby](https://www.gatsbyjs.org/) ，但这些步骤将适用于任何类似的 javascript 框架，如 [Vue](https://vuejs.org) 或 [React](https://reactjs.org/) ，甚至只是一个普通的 html/css/js 应用程序。如果你是 dev 的新手，可能值得看一看 CLI 并阅读一些文档，看看如何更改页面以获得你想要的页面，但在这篇文章中我不会对此进行太多的详细介绍。

我们还将使用 [Github](//github.com) 、 [Netlify](https://www.netlify.com) 、 [Cloudflare](https://www.cloudflare.com/) 和 [Namecheap](https://www.namecheap.com/) 来购买/配置域名。任何域名提供商都可以，但在本教程中，我将展示如何配置 Namecheap。

使用这个堆栈的好处是，您可以使用 React/Gatsby 的能力创建一个静态网站，但可以在几个免费的主机提供商上托管它，甚至可以从 CDN 分发它，以进一步提高页面速度。唯一需要付费的是域名，即使这样，Netlify 也会为您提供一个随机生成的域名，如果您还没有准备好获得域名，您可以使用它！

### 创建你的盖茨比应用程序

创建一个 Gatsby 应用程序就像在终端中通过`npm install -g gatsby`全局安装 CLI(命令行界面)并使用`gatsby new gatsby-site`创建一个新站点一样简单(用您的项目名称替换 gatsby-site)。

创建完成后，将`cd`(更改目录)添加到项目中，并使用`git log`确认它已经创建了一个初始提交。如果你看到类似“盖茨比的初次承诺”这样的话，它就成功了。如果你有一大堆未暂存的文件，用`git add -A && git commit -m "Initial commit"`提交它们。

### 将存储库放到 GitHub 上

您需要将存储库推送到 Github，这样 Netlify 就可以加入到构建过程中。Netlify 与 GitLab 和 Bitbucket 配合得也很好，所以如果你更喜欢这些或者已经在使用它们，它们也可以很好地工作。

如果你还没有 GitHub 帐户，你可以在这里免费创建一个。一旦创建了一个存储库，就用项目的名称创建一个新的存储库。您可以在这里写一个项目的简短描述，并选择您希望它是公共的还是私有的存储库。跳过初始化步骤，因为我们是从现有的回购导入。

一旦创建好了，你应该会得到一些方便的步骤来展示如何把一个现有的项目推上去，就像这样:

```
git remote add origin git@github.com:[your-user]/[your-repo].git
git push -u origin master 
```

在项目中遵循这些步骤，完成后刷新 GitHub 存储库页面。此时，您应该可以在存储库中看到您的文件。

### 将您的项目连接到网络生活

如果您还没有 Netlify 帐户，您可以在这里免费创建一个。我发现与 Github 账户的整合非常完美，所以可能值得注册你的 GitHub 账户(或者 Gitlab/Bitbucket，如果你正在使用的话)。

如果你不想使用像 AWS/Surge.sh 这样的 Netlify，有几个不错的选择。

一旦你登录到 Netlify，你应该会看到一个漂亮的绿色按钮，上面写着“来自 Git 的新站点”。单击此按钮将带您进入一个页面，您可以在其中选择存储库的托管位置。在这种情况下，我们将单击 GitHub。然后会提示我们从我们的帐户中选择一个存储库。选择我们之前创建的那个。

您可以选择部署哪个分支，默认为 master，这样通常就可以了。然后，您可以指定生成设置，如生成命令和发布目录。默认情况下，这通常会成功，但以下是几个例子:

*   盖茨比>构建命令:`gatsby build`，发布目录:`public/`
*   Vue (CLI 3)>构建命令:`npm run build`，发布目录:`dist/`
*   React(创建 React App)>构建命令:`npm run build`，发布目录:`build/`

如果您有任何不想包含在源代码控制中的环境变量，您可以在高级构建设置下添加它们。

一旦一切正常，单击 deploy site。然后，你会得到一个 netlify.com 域名的项目仪表盘，比如[https://stupefied-wilson-cafe46.netlify.com](https://stupefied-wilson-cafe46.netlify.com)。

### 获取域名

在过去的几年里，我一直在使用廉价的服务，他们提供的服务给我留下了深刻印象。搜索你喜欢的域名，一旦购买，进入域名列表，找到你的域名，点击管理。让它保持开放，因为我们很快就会回来

### 将您的域连接到 Cloudflare

这一步是可选的，但强烈推荐。Cloudflare 有一个慷慨的免费计划，其中包括一个 CDN，在 Netlify 因某种原因停机时始终在线的应用程序版本，以及针对 DDoS 攻击的保护，对流量的洞察等等。如果您还没有帐户，您可以在这里创建一个[。](https://dash.cloudflare.com/sign-up)

登录后，单击“添加站点”按钮。系统会提示您输入域名。点击添加站点，等待它查询您的 DNS 记录，然后继续。你应该得到一个屏幕，显示所有当前的 A/CNAME/MX/TXT 记录等。

点击继续后，您将看到两个名称服务器，如`lucy.ns.cloudflare.com`和`piotr.ns.cloudflare.com`。复制这些并再次进入你的廉价管理面板。在您的域设置下，您应该会看到一个标有名称服务器的部分。将其从“Namecheap BasicDNS”更改为自定义 DNS，并从 cloudflare 粘贴两个名称服务器。

### 将 Netlify 挂接到 Cloudflare

现在您已经使用 Cloudflare 设置了 DNS，您可以让它指向您的 netlify 站点。为此，请转到 Cloudflare 中的 DNS 选项卡。在这里，你应该有一个指向你的域名的 A 记录和一个指向“parkingpage.namecheap.com 的别名”的 CNAME(如果用 namecheap 注册的话)。删除指向停车页面的 CNAME。

转到您的网络管理面板，并在您的项目的仪表板下，单击设置，然后域管理。在标题为自定义域的部分，将有一个按钮来添加自定义域。单击此处并输入您的域名；它应该说该域名已经有一个所有者，确认这一点，并等待网站完成检查状态。

你会得到一个警告信息，说“检查 DNS 配置。”点击此处，从`Alternative: point A record to [ip-address]`块复制 IP 地址。

回到 Cloudflare，修改您的域的 A 记录以指向复制的 IP 地址，并创建一个新的 A 记录，以`www`作为名称，以 IP 地址作为值。

在加密选项卡下，打开`Always Use HTTPS`和`Automatic HTTPS Rewrites`

**搞定！**一切正常，在等待一段时间(通常最多 24 小时)DNS 记录更新后，您的站点将在您的域名上运行。任何时候您将提交推送到主分支，都会触发 Netlify 上的构建，它会立即构建和部署自己。

### 奖金

如果您想从您的地址中消除 www，您可以在 Cloudflare 中设置一个页面规则，值为`https://www.your-domain.org/*`，设置为`Forwarding URL`、`302 temporary redirect`，值为`https://your-domain.org/$1`。也可能值得检查一下 Cloudflare 的设置，因为那里有一些非常有趣的东西。

### TL；博士；医生

**Gatsby** -创建一个现成的应用程序，准备工作并使用 React
**GitHub** -托管您的 Git 存储库
**Netlify** -当提交被推送到您的存储库中的主分支时，连接到 GitHub 进行构建
**Namecheap** -购买并管理域名
**Cloudflare** -让它处理您的 DNS 并提供 SSL 证书/Brotli 压缩/Always 之类的东西