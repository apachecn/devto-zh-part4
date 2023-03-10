# 用 GitHub 页面托管

> 原文：<https://dev.to/rightfrombasics/hosting-with-github-pages-1cc1>

你知道你可以用 GitHub 页面托管网站吗？事实上，到目前为止，我所有的网站都是用 GitHub 页面托管的。看我的[项目](http://harishv7.github.io/projects)。域名 [harishv.me](http://harishv.me) 下的所有网站都托管在 GitHub 页面下。来看看为什么这么牛逼。

### 伟大的东西

#### 1。免费的！

GitHub Pages 为您的网站/应用程序等提供免费托管。没错，完全免费。(不过有一个问题，查看下面的限制)。

#### 2。连接到您的 Git 存储库

版本控制对你做的任何项目都是至关重要的。GitHub Pages 与您的 GitHub 存储库相连，这意味着一旦推送任何更改，您的网站就会自动重建并为您部署。看到- [我的博客](http://harishv.me/blog/)、[我的项目](http://harishv.me/projects/)、[我的摄影页面](http://harishv.me/photography/)的各个子页面都是独立的 GitHub 库。主页是一个名为`<your-github-username>.github.io`的 GitHub 存储库，位于`master`分支上。要在 GitHub Pages 域下部署其他存储库，您只需将您的更改推送到一个`gh-pages`分支。

#### 3。自定义域名

默认情况下，GitHub Pages 提供了域名`http://<your-github-username>.github.io`。这对于许多爱好者来说已经足够好了，他们可以尝试在 web 上部署他们的本地项目。但是如果你想要一个自定义域，你也可以这样做。只需在像 [Namecheap](http://namecheap.com/) 或 [GoDaddy](http://godaddy.com/) 这样的网站上注册一个域名，并将它们连接到 GitHub 页面。我的站点域名 [harishv.me](http://harishv.me) 在 Namecheap 上注册，并连接到我的 GitHub 库。

#### 4。Jekyll 博客支持

GitHub Pages 支持 Jekyll，这意味着您可以创建博客并部署它们。Jekyll 是一个强大的基于 [Markdown](https://en.wikipedia.org/wiki/Markdown) 的博客工具。你所需要做的就是在写完文章后运行几个 Jekyll 命令，Jekyll 就会创建静态站点，你可以很容易地将它们推送到 GitHub。我的[博客](http://harishv.me/blog/)也是用哲基尔建的。对于喜欢纯代码风格博客的人来说，这是一个很好的方式。此外，你所有的帖子都可以很容易地备份，因为它们都是基于文本的降价格式。

#### 5。出色的正常运行时间

GitHub 几乎总是会出现一些罕见的停机情况。这意味着它提供可靠的服务，确保您的网页 99%的时间都是实时的。这是免费服务的一大好处。你可以在这里查看 GitHub 系统状态[。](https://status.github.com/)

### 局限性

这是一个相当短的部分，因为我想不出太多的缺点。GitHub 页面有相当多的限制。

#### 1。仅托管静态站点

虽然 GitHub Pages 确实提供免费托管，但它不支持成熟的动态网站。相反，你只能托管静态网站，这意味着它们是纯 HTML，CSS 和 JavaScript。本质上，没有服务器端的支持，例如，你可以做数据库相关的操作等。然而，对于某些项目来说，这已经足够强大了。我所有的纯前端项目都是用 GitHub 页面托管的。我结合 HTML、CSS、JavaScript、ReactJS 和 jQuery 等库以及 Bootstrap 或 Foundation 等框架完成了许多这样的工作。

#### 2。仅支持 GitHub 存储库

当然，GitHub 只支持来自自身的存储库。如果你使用另一个平台如 [Atlassian BitBucket](https://bitbucket.org/) 进行版本控制，那么你将无法使用 GitHub 页面。如果你正在使用 BitBucket，你是幸运的，因为他们也提供静态站点托管支持。查看此[常见问题部分](https://confluence.atlassian.com/bitbucket/publishing-a-website-on-bitbucket-cloud-221449776.html)了解更多信息。

### 找托管？

*   如果你想要最好的 Wordpress 托管，我强烈推荐 [Bluehost](https://www.bluehost.com/track/harishv/) 。当你[通过此链接](https://www.bluehost.com/track/harishv/)申请时，可以获得很大的折扣。
*   此外，当你[通过此链接](https://www.hostg.xyz/aff_c?offer_id=6&aff_id=2883)申请时，还可以获得 [Hostinger](https://www.hostg.xyz/aff_c?offer_id=6&aff_id=2883) 的大幅折扣。

这两项服务都提供了良好的正常运行时间和客户服务。哦，我有没有提到-你得到一个免费的域名与 Bluehost 和 Hostinger！