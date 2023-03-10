# 通过 GitHub 部署到 PythonAnywhere

> 原文：<https://dev.to/aadibajpai/deploying-to-pythonanywhere-via-github-1j7b>

**每个人都可以这样做:**

本地→ github

**通过 ssh 访问(付费)，您可以这样做:**

本地→ pythonanywhere

**我这里做的是(免费):**

本地→ github → pythonanywhere

我首先列出为什么你可能想这样做，然后去如何做。如果你想直接跳到 how，那就随便跳。

### 为什么？

PythonAnywhere 太棒了。它是免费的，好的资源，甚至是一个数据库，你可以在几分钟内拥有一个动态的网站。如果你刚开始，只是想玩玩，或者甚至想托管一个 API 或你自己的东西，这是一个非常好的资源。

但是它也有缺点，如果你想开源你正在做的东西呢？你是不是维持两个分开的地方，一直犯两次？一次是在 PythonAnywhere 进行生产，另一次是在 GitHub，让人们检查你的代码。如果您合并一个拉取请求或想要集成 CI，该怎么办？一遍又一遍地做同样的事情真糟糕。

GitHub 非常适合协作，甚至是查看代码，他们有比 PythonAnywhere 更好的 UI，我的意思是，直接在 PythonAnywhere 上编辑代码不是一个好的体验。如果有一种方法能把所有这些联系起来呢？

你按下 GitHub，它会更新你在 PythonAnywhere 上运行的 web 应用程序，并重新加载它。你可以合并 PR，使用 Issues，从任何地方浏览你的代码，甚至不用登录并在编辑器中打开文件，以及 GitHub 提供的一切。

我听到你说，“好吧，Aadi，所有这些听起来都很好，但是怎么做呢？”

不要再说了。

### 如何？

我们使用 GitHub webhooks 来告诉我们的应用程序它已经被更新了，所以它执行一个 pull，然后重新加载它自己。

我将以我的应用程序 SwagLyrics 为例，它的后端托管在 PythonAnywhere 上。我用的是 Flask，所以如果你不是的话，你可能要做相应的调整。这是我的回购，所以你可以用它作为参考:

[swag lyrics/swag lyrics-发行人](https://github.com/SwagLyrics/swaglyrics-issue-maker)

首先，确保你在 GitHub 和 PythonAnywhere 上的仓库是同步的，GitHub 应该是原点，如果你还没有在 PythonAnywhere 上做一个 repo，你可以初始化一个或者从 GitHub 克隆到 PythonAnywhere 上。

类似于

```
git init

git remote add origin https://github.com/yourusername/yourreponame.git 
```

Enter fullscreen mode Exit fullscreen mode

现在进入你在 GitHub 上的资源库→设置→ Webhooks →添加 webhook。

这就是你看到的:

[![](img/84517d4e4f0c8ed634eb4d8a22cb96d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mw8d6Sxe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/928/1%2AWj_3-2NZr2g2hcN0R5Imvw.png)

在有效负载 URL 中添加[https://your _ domain/route _ to _ update](https://your_domain/route_to)_ update

我的回购协议将网络挂钩发送到了 https://aadibajpai.pythonanywhere.com/update_server

然后将内容类型从 application/x-www-form-urlencoded 更改为 application/json(稍后我会告诉您原因)。

我们稍后也会谈到秘密部分。

确保是 push 事件触发了 webhook，然后点击 Add webhook。

现在，打开你的 flask 应用程序，我们将配置一个路由，每当有推送事件时，它就从 GitHub 接收信息。这个路由将与我们在有效负载 URL 中使用的路由相同。我们没有显式签出 master，因为我假设这是这里唯一的分支。

一个基本的简单设置应该是这样的:

```
from flask import Flask, request
import git

app = Flask(__name__)

@app.route('/update_server', methods=['POST'])
 def webhook():
  if request.method == 'POST':
    repo = git.Repo('path/to/git_repo')
    origin = repo.remotes.origin

   origin.pull()

   return 'Updated PythonAnywhere successfully', 200
  else:
   return 'Wrong event type', 400 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常基础的版本，下面还有一个更完整的版本。

现在，每当有 push 事件时，该路由将接收信息并通过 pull 进行自我更新。

希望这是您提交时的样子

<figure>[![](img/db7aaa50bb31d43302791bf1fb159827.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jJst1_OU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/944/1%2Ard5EjC611DIWS_Stf_dBlw.png) 

<figcaption>十指交叉</figcaption>

</figure>

在我告诉你如何保护你的 webhook 之前，我会告诉你如何确保你的应用程序在一次拉取后重新加载，这样你就不必手动这么做了。

#### 自动重新加载您的 webapp

我们将使用 git 钩子来实现它。这些是在 git 事件发生后执行的 shell 命令。没有后拉钩，但是…

我们利用了这样一个事实，git pull 只不过是 git fetch → git merge，并且有一个 [post-merge](https://git-scm.com/docs/githooks#_post_merge) 钩子。如果拉操作成功完成，它就会执行。

在你的皮托纳仓库里。git/hooks/

将会有一堆预先存在的文件，但会创建一个名为后合并的新文件。

将下面的代码放在这里:

```
#!/bin/sh
touch /path/to/username_pythonanywhere_com_wsgi.py 
```

Enter fullscreen mode Exit fullscreen mode

使用您的 wsgi 文件的路径，当触摸该文件时，会重新加载您的 webapp。

现在要使这个可执行文件生效，打开一个 bash 控制台并运行

`chmod +x post-merge`

就这样，现在您可以通过提交一个示例来测试它是否工作。

最重要的是，现在我们继续保护你的网钩。

### 保护你的网络钩子

这是必要的，这样其他人就不能向您的服务器发送垃圾邮件，请求更新它。

我们跟随这个指南去了解它[https://developer.github.com/webhooks/securing/](https://developer.github.com/webhooks/securing/)

首先，导出 secret 令牌并将其作为环境变量添加到 PythonAnywhere 以及 GitHub webhook 设置中 Secret 字段。这可能会有所帮助[https://help . python anywhere . com/pages/environment-variables-for-web-apps](https://help.pythonanywhere.com/pages/environment-variables-for-web-apps)

现在，GitHub 用 Ruby 列出了他们的方法，但是我们用 Python，所以虽然方法是一样的，这里是比较函数: