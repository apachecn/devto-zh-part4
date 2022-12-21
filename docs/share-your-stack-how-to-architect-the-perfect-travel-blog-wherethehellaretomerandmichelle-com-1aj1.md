# 分享你的书库——如何设计完美的旅游博客，WhereTheHellAreTomerAndMichelle.com

> 原文：<https://dev.to/thetomester13/share-your-stack-how-to-architect-the-perfect-travel-blog-wherethehellaretomerandmichelle-com-1aj1>

当 Michelle 和我开始一起策划我们的旅行计划时，我知道我想要成长，无论是作为一个人，还是作为一个开发者。我开始成长的第一步是选择一种新的技术来托管我们的旅游博客。我已经和 WordPress 合作过了，在 Dev.to 上发表过，我知道我想托管一些东西(即非媒体)。

## 要求

除了基本的博客必需品，我的要求是:

*   静态-这将是一个简单的网站，我想让它重量轻-没有必要在这里服务器端提升。
*   易于更新——我希望能够轻松地更新它，并消除发布新帖子或更新网站的任何障碍。一旦去了 CI/CD，就再也回不去了！
*   Markdown 中的帖子——Markdown 很简单，现在到处都是。如果你想/需要将你的文章迁移到不同的栈，这也是一个将你的内容从你的站点中分离出来的好方法。

很长时间以来，我一直听说 Jekyll 和 Github 页面托管以及这两种技术之间的简单性和集成。这似乎是拿起它跑起来的最佳时机！

我为我们的网站使用了[拉格朗日哲基尔主题](https://lenpaul.github.io/Lagrange/),因为它看起来很简单，我们喜欢它的外观(人们是如何挑选东西的？？).我继续前进，分叉的项目，然后开始把我们自己的触摸它。

## 基本设置

Jekyll 使得在`settings.yml`文件中定制站点的某些部分变得非常容易。我们确保更新我们的社交链接，关于页面、联系人等。

我们当然也想要我们自己的域名，而不是 github.io 子域。谁会想到`WhereTheHellAreTomerAndMichelle.com`是可用的？？有了我最喜欢的域名注册商， [NameCheap](https://namecheap.com/) ，以及 13 美元，我们成为了骄傲的新主人！Github Pages 使得在自定义域名上托管您的站点变得容易，只要存储库是公开的。使用您的存储库的页面设置，它将通过在您的存储库中创建一个 CNAME 文件来配置您的自定义域名。Github Pages 还会自动打理一个 SSL 证书，想得真周到！我们去参加比赛了！

## 旅游化吧

因为这毕竟是我们的旅游博客，我们知道我们需要把我们的行程和图片幻灯片放在上面。对于交互式地图/旅程，我们选择了[TravellersPoint.com](https://www.travellerspoint.com)的嵌入式地图。我以前用它们吃饭、祈祷、编码旅行，喜欢它们提供的东西。为了进一步定制嵌入地图，我升级为“萌芽成员”,并将嵌入代码直接放在 Jekyll 页面中。由于这是一个`iframe`，我还添加了一些 Javascript 和适当的样式，以确保地图在移动和桌面视图上都能很好地工作。

图片稍微复杂一些。第一部分是弄清楚在哪里存放我的照片。我希望能够只上传选定的照片，不必担心存储限制(至少在可预见的未来)，并且能够共享公共相册。最终 [Google 相册](https://photos.google.com/)胜出，因为他们能够公开分享相册。(我仍然欢迎其他照片存储/共享解决方案的建议！)

谷歌照片的下一个问题是，我想在我们的主页上创建一个自动更新的嵌入式幻灯片。棘手的部分在于 Google 相册缺乏 API。幸运的是，我看到了这个页面，在这里，给定一个可公开访问的 Google 相册，它将创建一个嵌入式幻灯片小工具。目前这不会自动更新，当我发布新照片到共享相册时，我只需要不时地更新滑块代码。

## 发布帖子

对于这个新的堆栈，我真正期待的一件事是在 Markdown 中发布帖子。作为一名开发人员，我喜欢它的简单和无处不在，同时仍然让作者控制他们可能需要的任何元素。当我开始创建一个新帖子时，我只需打开我选择的 Markdown 编辑器， [MacDown](https://macdown.uranusjr.com/) ，开始输入！当我完成一篇文章并由 Michelle 进行最后的润色和编辑时，我可以使用一个简单的`git push`命令来发布它！

## 注释

我敢肯定，在我们的 WWW 探险中，我们都曾在某个地方遇到过 Disqus，这就是我最初为我们的网站设置的。但鉴于我是一个自助主持鉴赏家，当我听说 [Commento.io](https://commento.io/) 时，我立即知道我想试试。幸运的是，不久之后， [Cloudron](https://cloudron.io/) 团队在他们的平台上推出了 Commento.io 自托管应用。我在我的 Cloudron 实例上设置它，从 Disqus 实例导入所有数据，并用适当的 Commento 嵌入脚本替换注释代码。维奥拉。现在，您可以在我们的网站上发表自主、安全、注重隐私的评论。

## 分析学

Google Analytics 可能是许多网站和博客的首选，但我一直在使用也是自托管的 [Matamo](https://matomo.org/) (以前的 Piwik)。使用 Matamo Analytics 仪表板来建立一个新的网站是轻而易举的事情。然后，简单的用 Matamo 的代码替换 Jekyll 主题默认提供的 Google Analytics 代码，推上去！

## “但是被公交车撞了怎么办？”

米歇尔，出于她的爱和体贴，有一天问我，“如果你死了，我如何发表新帖子？”她说得有道理。她不仅不是开发人员，也从未使用过 Github，而且该网站的知识库也只在我的用户名下。

经过快速搜索，我发现了[forestry . io](https://forestry.io/)——一个 Jekyll CMS。这似乎是从 [Jekyll Admin](https://jekyll.github.io/jekyll-admin/) 派生出来的，但无论如何，Forestry 都是一个神奇的工具，可以通过托管媒体、直接集成到 Github 和 Github 页面，当然还有创建和编辑帖子的方式，让管理 Jekyll 网站变得更加容易。是的，他们确实为个人网站提供了慷慨的免费计划！设置这是一件轻而易举的事情，唯一稍微棘手的事情是正确设置媒体文件夹，但即使这样也没有任何问题。由于每个网站支持多个用户，米歇尔和我都可以通过他们的 CMS 平台更新我们的 Jekyll 网站，包括撰写和发布帖子。

## 栈鸟瞰

*   域名:[名称便宜](https://www.namecheap.com/)
*   托管，与 HTTPS: [Github 页面](https://pages.github.com/)
*   框架:[哲基尔](https://jekyllrb.com/)
*   哲基尔主题:[拉格朗日](https://github.com/LeNPaul/Lagrange)
*   分析: [Matamo](https://matomo.org/) (自托管)
*   评论: [Commento](https://commento.io/) (自主持)
*   互动地图:[TravellersPoint.com](https://www.travellerspoint.com/)
*   照片托管和共享相册: [Google 相册](https://photos.google.com/)
*   CMS: [Forestry.io](https://forestry.io/)

就像生活中大多数美好的事物一样，这需要时间，而且不会永远进化下去。你可以查看一下 Github 库，亲自看看上面的每一项是如何随着时间的推移而发展和实现的。当然，我对我想在博客上写什么有一个大致的想法，但是很多想法需要时间去发展和实现。[一定要看看最终产品](https://WhereTheHellAreTomerAndMichelle.com)(目前)！