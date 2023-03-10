# 过度复杂化我们的 mvp 是如何杀死 snappier 的

> 原文：<https://dev.to/m1guelpf/how-overcomplicating-our-mvp-killed-snaptier-38p2>

如果你去年夏天在 Twitter 上关注过我，你可能听说过[snappier](https://snaptier.co)。在最终独立推出 [UnMarkDocs](https://unmarkdocs.co) 之后，我想尝试一些新的东西。在那之前，我所有的项目都是独立完成的，所以我联系了[多米尼克](https://twitter.com/dmfj)(朋友和青少年程序员同事)，我们开始为一个新项目集思广益。

我们最初想为 Composer 软件包建立一个市场(与刚刚推出的的【Packagist 的想法相同)，但在我得到上述项目的发展通知后，我们决定转向。

[![How overcomplicating our MVP killed Snaptier](img/61e2d6ff9e7284827c3d36e839231c43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pmIpCBZT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/Image-2019-06-02-at-2.31.12-PM.png)

在集思广益之后，我们决定寻求一种能够自动完成常见编程任务的服务。这个想法是让某些任务变得简单，比如连接你的 GitHub 账户和合并一个 PR(有点像 [Laravel Shift](https://laravelshift.com) 所做的，但是是为了其他类型的事情)。

在开发这个应用程序的时候，很明显我们的设计技能并没有我们想要的那么好，我们决定引入 Niklas，他是我之前见过的一个很棒的设计师。

[![How overcomplicating our MVP killed Snaptier](img/6941da460801b951690fc7d2d70aa61d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B3SArRdK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/Image-2019-06-02-at-2.39.46-PM.png)

当 Niklas 处理 UI 和前端以及 Dominic 管理的服务器时，我开始创建一个后端框架，将所有 GitHub 逻辑抽象成一个易于使用的流畅界面。这被证明是一个可怕的想法，因为它减慢了产品的整个开发速度(我将在后面详细介绍)。

我们整个夏天都在继续构建 Snaptier，我们度过了一段美好的时光。我通常在 Twitch 上进行现场编码，我们每周开一次会，在我们的 Discord insiders 频道上与社区一起开发内部笑话，甚至录制了一些播客片段(尽管我们从未发布过)。

[![How overcomplicating our MVP killed Snaptier](img/141070024662c1221a5cb07ee733fde6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U8wsghAV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/snaptier.wip_.png)

[![How overcomplicating our MVP killed Snaptier](img/96fdd4cf1e4d6702fd02e266c3b9361e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pmIAyIJh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/building.snaptier.wip_.png)

[![How overcomplicating our MVP killed Snaptier](img/3eac933d70d1f91da8fb33f5dbb68fe7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WhE2YCKG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/snaptier.wip_coming-soon.png)

[![How overcomplicating our MVP killed Snaptier](img/ff81850b47e5102a6f0b13815facaa8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MiNlTo8s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/snaptier.wip_dashboard.png)

[![How overcomplicating our MVP killed Snaptier](img/8270e5f24ddc2231ee7d8355924a619a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CICN3ZkA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/snaptier.wip_login.png)

[![How overcomplicating our MVP killed Snaptier](img/186209ffafeb3fbdf3690751bdb4329d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nNBRkXj6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/snaptier.wip_settings_account.png)

<figure>

[![How overcomplicating our MVP killed Snaptier](img/90cebe4e24f3bf7bc1cb4c541d76eb19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GDnXyFTs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/snaptier.wip_settings_account--1-.png)

<figcaption>Never-seen-before screenshots of the Snaptier UI, designed by [Niklas](https://twitter.com/shroudedcode)</figcaption>

</figure>

还记得我之前说的那个流畅的界面吗？我花了一个夏天的大部分时间来开发它，使它异步并改进它，但仍然没有让一切正常工作。到了不抛出异常的地步，几乎不可能调试任何东西。我不得不强迫自己去做这件事，直到我完全筋疲力尽，开始做其他事情。

Niklas 和 Dominic 也做了同样的事情，开发暂停了，除了我们没有人知道。每个人都继续进行新项目的工作，但是我们完全忘记(或者拖延)告诉那些从一开始就跟踪产品的人。

[![How overcomplicating our MVP killed Snaptier](img/01ed29aa5d6e765a1adfacd604286073.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DI3pc91S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/Image-2019-06-02-at-3.37.54-PM.png)

我们最终决定在三月份终止这个项目，但出于某种原因，我们拒绝公开说出来。我想这是你投入了大量生命的东西宣告死亡的情感因素。

<figure>

[![How overcomplicating our MVP killed Snaptier](img/abf137e8d484bdc49c7b3eb6384a301c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TtLCxszx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/Image-2019-06-02-at-3.51.29-PM.png)

<figcaption>We internally decided to announce Snaptier was dead, but still no one said anything publicly</figcaption>

</figure>

然后，今年 5 月，我又开始做后端。除了证明自己可以完成我花了这么多时间构建的抽象之外，我没有理由这么做。我终于设法让它工作起来，并制作了我们的第一个公关(可能也是最后一个)。

<figure>[![How overcomplicating our MVP killed Snaptier](img/4bdae2f0b30ab9a8ccadb52f35a98fd3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L06Pq6DM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/07/Image-2019-06-02-at-3.57.34-PM.png) 

<figcaption>当我推送使一切工作的代码时我发送的内部消息</figcaption>

</figure>

尽管如此，我们还是不愿意重启这个项目。我们还让我们的原始域名(snaptier.co)过期，有人抢去了它，并将其重定向到一些八卦网站。考虑到这一切，尼克拉斯写了一篇文章，并在公共频道上发布了一篇评论。

<figure>[![How overcomplicating our MVP killed Snaptier](img/3864da5ecb8eb8afcbe7faa0fc121190.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6IXkxlXd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/Image-2019-06-02-at-4.01.56-PM.png)

<figcaption></figcaption>

</figure>

Niklas 已经总结了我们学到的所有经验教训，但在我看来，最重要的一条是**MVP 应该是短小的版本，你可以稍后在**上迭代。我将这种想法应用到我的下一个产品 [Blogcast](https://blogcast.host) ，并在大约一周内得到了一个初始的、可发布的版本[。](https://miguelpiedrafita.com/building-blogcast)

<figure>

[![How overcomplicating our MVP killed Snaptier](img/d1099dcebcf9c51c84846b7d418342f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6HxsVoHi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2019/06/Image-2019-06-02-at-4.06.28-PM.png)

<figcaption>How building products should work. Stage 1 is the MVP and the next stages represent iterations. The benefit of building like this is that people can use your product since the MVP and don't have to wait until the full-fledged version is ready. Illustration from [Henrik's fantastic post on the topic](https://blog.crisp.se/2016/01/25/henrikkniberg/making-sense-of-mvp).</figcaption>

</figure>

尽管结局不尽如人意，但我认为 Snaptier 是成功的。我玩得很开心，也学到了很多，既有关于代码的，也有关于如何构建产品的。我希望有一天我能回去完成 Snaptier，但现在，这是一个重要的教训。

*你可以在 Twitter 上关注* [*多米尼克*](https://twitter.com/dmfj) *和* [*尼克拉斯*](https://twitter.com/shroudedcode) *并查看他们的新的、令人敬畏的产品*[*song mash*](https://songmash.app)*。你也可以阅读更多关于[我如何在不到一周的时间里开发出我的下一个产品](https://miguelpiedrafita.com/building-blogcast)和[给 Blogcast 一个尝试](https://blogcast.host)。*