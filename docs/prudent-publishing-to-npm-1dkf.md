# 向国家预防机制谨慎发布

> 原文：<https://dev.to/rowan_m/prudent-publishing-to-npm-1dkf>

这篇文章分享了一些实践，我发现这些实践有助于保持我将包发布到 npm 时与我的个人环境更加隔离。我认为这里有很好的原则，但它们可能有点特定于我的项目和平台。请针对您自己的环境提供变体或提示。嘿-也许你可以自己写一整篇文章！

# 💌使用单独的电子邮件注册 npm

对于大多数开发者渠道来说，这是一个很好的做法——无论是在 Play Store 发布一个 Android 应用程序，你所依赖的特定 API 的帐户，还是你的 npm 帐户。这样做有很多原因:

*   ⛔账户被误封禁了！作为 npm 上的一个人，您与拥有您的包的帐户是不同的。如果一个被禁，不会影响另一个。
*   🤝其他人可能需要访问。虽然从长远来看，[组织](https://docs.npmjs.com/orgs/)是正确的解决方案，但有时会出现紧急情况或一次性情况，共享一个特定帐户的访问权限肯定比您的个人帐户要好！
*   👔这封邮件可能需要公开。你可能想提供支持，或者你只是得到随机查询。使用单独的地址可以让你把它当作自己的事情来管理。

这个没有真正的说明...无论你想在哪里找到那个电子邮件地址！

# 🎭使用单独的本地用户发布

这比使用单独的电子邮件有更明显的安全好处(坦白地说，更多的是关于你的个人幸福)。

这里的大部分问题来自于其他软件包受到危害和注入恶意代码。这里的两个具体事件是一些 [ESLint 包，包括窃取 npm 令牌的代码](https://eslint.org/blog/2018/07/postmortem-for-malicious-package-publishes)和 [`event-stream`包被入侵以窃取加密货币](https://blog.npmjs.org/post/180565383195/details-about-the-event-stream-incident)。创建一个独立用户有助于通过减少暴露来缓解这种情况。因此，举例来说，如果有一个被利用的软件包能够在部署过程中启动，它将无法访问您的个人用户。同样，开发环境中被利用的包将无法访问您的部署过程。

如果你真的很认真，那么你真的应该确保所有的 npm 操作都作为一个独立的用户运行，但这确实会带来很多开销。

我是一个 Linux 用户，所以这些指令都基于我的 Bash shell 环境。如果您自己的平台有任何改进，请分享！

🐣好的，让我们创建我们的用户:

```
$ sudo adduser deploy-npm 
```

Enter fullscreen mode Exit fullscreen mode

系统会提示您创建密码。明明用的是固体的东西，我们却不需要管它是什么。当我们访问它时，我们将使用`sudo`。

🕵️:现在我们需要为新用户配置环境。我们将使用`sudo`登录到那个账户:

```
$ sudo -i -u deploy-npm 
```

Enter fullscreen mode Exit fullscreen mode

用户需要访问节点和`npm`的版本。我使用`nvm`来管理它，所以我做的第一件事就是按照他们的[安装说明，获得节点](https://github.com/nvm-sh/nvm#installation-and-update)的 LTS 版本。你可以想怎么做就怎么做。例如，可能您已经在系统范围内安装了 Node，因此新用户可以立即使用它。

☑️通过检查`npm`版本
来验证你的设置

```
$ npm -v 
```

Enter fullscreen mode Exit fullscreen mode

👋现在该登录账户了！不出所料，那是用:

```
$ npm login 
```

Enter fullscreen mode Exit fullscreen mode

🔐如果您还没有在您的帐户上启用双因素身份验证，这是一个理想的时机！这意味着，即使您的帐户受到威胁或令牌被盗，如果没有第二个身份验证机制，攻击者仍然无法做任何事情。

```
$ npm profile enable-2fa auth-and-writes 
```

Enter fullscreen mode Exit fullscreen mode

💾我们还需要回购协议的本地副本。我们的部署用户不会提交任何东西，也没有设置 SSH 凭证，所以我们将使用 HTTP 版本。这假设您的代码在 GitHub 上，但是任何只读签出都是您在这里的目标。

```
$ git clone https://github.com/someUser/my-package.git 
```

Enter fullscreen mode Exit fullscreen mode

🔏这里的假设是，您已经适当地标记了准备发布的回购。因此，让我们进入目录并检查标签。

```
$ cd my-package
$ git checkout tags/0.0.1 
```

Enter fullscreen mode Exit fullscreen mode

🎁所有的设置都完成了，现在我们发布！假设您启用了 2FA，您将在这里被要求输入您的确认码。

```
$ npm-publish 
```

Enter fullscreen mode Exit fullscreen mode

对于我的使用，我已经把它包装在一个脚本中，我可以用它来发布一个给定的标签。第一个是作为`deploy-npm`用户运行的，实际上就是我们上面使用的命令。我已经将它作为`deploy-npm.sh`保存在用户的主目录中。

```
#! /bin/bash
cd &&
source .nvm/nvm.sh &&
cd my-package &&
git fetch &&
git checkout tags/$1 &&
npm publish 
```

Enter fullscreen mode Exit fullscreen mode

最后，我在自己的帐户中有一个脚本，它执行适当的`sudo`调用，作为`deploy-npm`用户运行。

```
#! /bin/bash
sudo -H -s -u deploy-npm /home/deploy-npm/deploy-npm.sh $1 
```

Enter fullscreen mode Exit fullscreen mode

🤖现在，在我标记了一个发布之后，我只需要运行:

```
$ deploy-npm.sh 0.0.2 
```

Enter fullscreen mode Exit fullscreen mode

如果你还在寻找更好的方法，试试 Liran 的这篇文章，里面有一些其他有用的建议。

[![lirantal](img/203bfc277b771b4d2c7d67ce884abcf4.png)](/lirantal) [## npm 安全提示，让您远离恶意模块

### liran Tal Aug 19 ' 192 分钟阅读

#npm #node #security #javascript](/lirantal/npm-security-tips-to-keep-you-safe-of-malicious-modules-25bp)