# 如何用 CircleCI 绕过 GitHub 页面的 jekyll-pagination-v2 限制

> 原文：<https://dev.to/mrpowerscripts/how-to-get-around-the-jekyll-pagination-v2-limitation-of-github-pages-with-circleci-16eg>

Github pages 是该死的现代奇迹。你可以从互联网上的 jazillion 模板中免费创建一个基本的网站。买个域名。你准备好了。在 GitHub 上发布对源代码的修改，他们会重新生成站点。太神奇了。几乎和 geocities 一样好。但是按照 GitHub 建议的方式使用 GitHub 页面有一些限制。Jekyll 有太多有用的插件，你可以在 GitHub 页面提供的 oens 之外使用。比如`jekyll-paginate-v2`插件。如果你想在 GitHub 页面上使用`jekyll-paginate-v2`插件，那你就不走运了！他们不支持。这意味着您不能对集合进行个性化设置——当您想使用 jekyll 构建更复杂的网站或博客时，这是一个非常有用的功能。但是我们可以通过一些脚本和 CircleCI 来解决这个问题。

如果你想知道脚本的所有技术细节，那么看看 Sangsoo Nam 的[博客帖子，它启发了我自动化这个。这真的写得很好，比我更好地解释了机制。但是为了跟上您的进度，我们基本上将 GitHub pages 站点配置为使用两个独立的分支。一个分支将包含我们 jekyll 站点的源代码。通常这个分支是默认的`master`分支，我们将站点变更推送到这个分支。这将启动 CircleCI 上的一项工作，它使用最新版本的 jekyll 及其所有可用插件来生成站点源代码。然后，它自动将生成的源代码推回到我们的 GitHub pages 分支，通常是`gh-pages`。然后 GitHub repo 被配置为向您的用户提供刚刚出炉的源代码。所以现在我们不局限于使用 jekyll 的托管版本和有限的允许插件。我们可以用任何东西。自由。](http://sangsoonam.github.io/2019/02/08/using-git-worktree-to-deploy-github-pages.html)

由于我们可以使用任何东西，我决定在网站上添加一些测试。在我做测试之前，我不知道有多少东西坏了。一个叫做`html-proofer`的 ruby gem 在我的网站上发现了 250 多个错误！呀。甚至一些非常破的东西，破坏了搜索引擎的网站，损害了页面的搜索引擎优化。既然我们在 CircleCI 上运行，我们可以在部署它之前向生成的站点添加简单的检查。所以首先我使用了`html-proofer`，它链接了所有站点的 html 结构。寻找破碎的 html 结构和不良做法。它甚至可以选择 lint 你的 opengraph 结构。我想检查的另一件事是我用作 API 的一些生成的 json 文件。我使用`find`来创建站点源代码中所有 json 文件的列表。然后我将它们传递给另一个名为`jsonlint`的 ruby gem，它确保在站点部署之前 json 结构是正确的。最后，您可以看到它对 CloudFlare 进行了 API 调用，以清除缓存。你不需要添加这个，但 CLoudFlare 真的很好，可以帮助加快网站的速度。他们有一个免费层，几乎可以在任何 GitHub 页面网站上使用。

所以这里所有的魔法都要感谢 CircleCI。基本上，当我们推送提交时，它会启动一个 CI 作业，执行 win Sansoo 的博客文章中概述的部署步骤。你现在知道了，因为你去看过了。而是给 TL；整个过程的 dr:当我们向 GitHub 提交时，它触发 CircleCI 上的一个作业。使用站点 repo CircleCI 中的上述配置，将从`master`检查代码。然后使用 jekyll 的最新版本和我们需要的任何插件来编译我们的 jekyll 网站。在我的例子中，它是`jekyll-paginate-v2`插件。然后它运行测试以确保站点结构和 json APIs 没有被破坏。最后，新编译的站点被推回到`gh-pages`分支，GitHub 将在 GitHub 页面上提供它。酷毙了。

除此之外，它仍然非常接近原剧本。除了现在整个过程是自动化的，所以不需要手动推送代码两次。