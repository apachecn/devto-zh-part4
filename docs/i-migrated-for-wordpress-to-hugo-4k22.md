# 我从 WordPress 迁移到 Hugo

> 原文：<https://dev.to/mainawycliffe/i-migrated-for-wordpress-to-hugo-4k22>

这篇文章晚了几个月——大约 6 个月——但是正如他们所说，晚做总比不做好。几年前，我用另一个名字——theinfogrid.com——创建了自己的博客，最初，它是一个普通的科技博客，但慢慢地我找到了自己的定位。我从一篇有棱角的文章开始，当反响不错时，我坚持了下来。

## 关于旧博客

我用 WordPress 建立它，有一个免费的主题。它每年花费我大约 20 美元，其中大部分花费在域名上。共享主机的成本大约是 5 美元。这不是很多钱，但如果你碰巧刚从大学毕业，没有工作，这也不是一个小数目。

几个月后，我的博客已经超出了我的廉价共享主机计划，因为我获得了一些不错的流量。这让我转向了虚拟专用服务器(VPS)–[AWS light sail](https://aws.amazon.com/lightsail/)。我开始用的是[5 美元计划](https://aws.amazon.com/lightsail/pricing/)，很快那也不够，升级为[10 美元计划](https://aws.amazon.com/lightsail/pricing/)。

这在短时间内运行良好，但至少每周一次，我的网站会随机离线。尽管使用了 [Cloudflare](https://www.cloudflare.com/) 代理来减少我的博客服务器的流量，并且做得非常好。当网站离线时，我不得不关闭我的 LightSail 实例，然后手动启动它。这个问题会在接下来的一周左右消失。对我来说，早上醒来发现我的博客关闭了一段时间是很平常的事。

## 寻找 WordPress 的替代品

前进的道路是升级到一个更大的计划。虽然这缓解了问题，但运营博客的成本却越来越高。对于没有带薪工作的人——我在一家初创公司工作——保持低成本至关重要。

我开始寻找替代方案。我考虑了一些解决方案，包括只用 WordPress 作为后端，Angular 作为前端。为了保持低成本，我会使用 CDN-[CloudFront](https://aws.amazon.com/cloudfront/)-积极地缓存 API 调用，因为我一周只发布 3 次。有一段时间，我也考虑过迁移到 Medium 或类似的站点。

我一直在考虑 Angular + WordPress 解决方案，直到我遇到了静态站点生成器(SSG)。这个解决方案的灵感主要来自于 [fireship.io](http://fireship.io/) ，当时杰夫·德莱尼(Jeff Delaney)决定在[创办一个新网站](https://www.youtube.com/watch?v=gun8OiGtlNc)，他开源了代码。在检查代码时，我遇到了 Hugo 并做了一些研究。

我比较了不同的静态站点生成器，最终选择了 Hugo。使用过它的人对它的评价很高，他们喜欢它是用 Go-情操建造的，我想是这样。我开始写我的新博客。

## 我的新博客

我很快学会了如何使用 Hugo，并且能够很快为我的博客建立自己的主题。这是因为，对于我喜欢的主题，我已经看到一些其他博客使用它们。有趣的是，我在 WordPress 上没有同样的问题。但是我猜这更多的是因为我对构建 WordPress 主题缺乏兴趣。出于某种原因，尽管我从事 PHP 工作多年，但我从未涉足其中。

回到我的博客，几周之内，我有了一个新的博客。我把它从[theinfogrid.com](http://theinfogrid.com/)更名为[codinglatte.com](https://codinglatte.com/)，并尽可能低调地推出了它。我只是碰巧不擅长社交媒体。

我能够成功地迁移，同时保持我在以前的博客中获得的谷歌搜索排名。事实上，谷歌搜索的流量增加了，几乎翻了一番。谷歌搜索占据了我博客的大部分流量。

这个博客目前由 Firebase 托管，运营成本非常低。Firebase 有一个非常好的免费计划，只有当你浏览它时才会被收费。因为我总是这样做，所以每月的花费大约是 1 美元。在接下来的几周里，我正在努力改进我的博客，以改善读者体验，为我的读者提供更多的价值。

WordPress 自带了一个不错的内置评论系统。它有很好的垃圾邮件检测功能，这是我真正需要的，因为我收到的评论中只有 1%不是垃圾邮件。有几个血统评论系统，但它们可能相当昂贵。我发现这很有挑战性。

我最终选择了[话语](https://github.com/utterance/utterances)部件，这是一个 GitHub 问题评论部件。简而言之，您为您的博客评论创建了一个 GitHub 存储库，每篇文章/帖子都会在存储库中出现一个问题。然后用户可以对这个问题发表评论。Utterances 会自动为您处理这个问题，您只需要将它指向正确的存储库。

## 弊端

然而，事情并不都是美好的，因为任何解决方案，Hugo 都不是完美的。在 WordPress 上我真的想念很多东西。第一个与 WordPress 插件生态系统有关，它有大量像样的插件来帮助你自动化任务和增加功能。另一方面，Hugo 和我猜测大多数 SSG，你需要为任务手动地做它并且建立你自己需要的功能。例如，在内容中插入广告或在改变文章路径后设置重定向。

另一个挑战与职位统计有关。有了 WordPress，如果你想添加最受欢迎的帖子部分，很容易。另一方面，和雨果在一起，你不能，至少不能直接。您需要构建一个数据 API，从一些分析服务中提取数据，并在构建期间将其公开给 Hugo。您还可以使用 JavaScript 来动态提取数据。

当与客户合作时，另一个挑战出现了。有了 WordPress，你需要做的就是向他们展示如何更新他们的内容。一旦他们更新了内容，这些内容就会自动反映在网站上。Hugo 另一方面，如果你想要一个内容更新被反映，你需要重建和部署。对我这样的人来说，这很容易，对客户来说就不那么容易了。有自动化这个过程的解决方案，比如使用 Netlify 和 Git 后端。这涉及到建立一个构建和部署管道，但是正如你所看到的，它不像 WordPress 那样简单。

不要误解我，我喜欢和 Hugo 一起工作，但这是和它一起工作的一些缺点——以及大多数 SSG。我只是指出一些在开始跳之前要考虑的缺点。对我来说，利大于弊。