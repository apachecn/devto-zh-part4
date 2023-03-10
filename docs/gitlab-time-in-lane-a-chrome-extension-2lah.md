# git lab Time in Lane:chrome 扩展

> 原文：<https://dev.to/jlengrand/gitlab-time-in-lane-a-chrome-extension-2lah>

[![Gitlab Time in Lane : a chrome extension](img/c40cbcba22a2107fd0c803d97d9ef347.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LDBZ2FmM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1531030874896-fdef6826f2f7%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

你好，

我在 ING 的团队不久前放弃了 SCRUM，取而代之的是更能消除的东西。我们还有很长的路要走，但我们正在努力:)。

我们搬迁的主要原因是，尽管我们只负责一个业务流程，但我们还要维护几个应用程序。这使得每次都很难找到一个单一的冲刺目标，因为我们通常会在生产中引入更小的、更多迭代的增量。

我们使用 Gitlab 板来监控我们的进度。这有一个巨大的优势，即能够将问题与合并请求结合起来，这很有意义。然而，**我们纠结的一件事是知道某人已经忙了多长时间。**

下图不太能说明问题例如，你不知道我花了多长时间来重构我的 Javascript:

[![Gitlab Time in Lane : a chrome extension](img/7f912202c2e0c144336abd13e4a5067a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m9DruBdl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lengrand.fr/conteimg/2019/06/Screen-Shot-2019-06-09-at-12.03.49-PM.png)

上周五晚上无所事事，我创建了一个 **[小 chrome 扩展](https://chrome.google.com/webstore/detail/jecodijiblfkhcgnllajecjpngbngaoi/publish-accepted?authuser=0&hl=en)** 来修复它。当扩展检测到你在 Gitlab 论坛上，**它会请求这个问题在同一个通道上停留的天数**。简单，但是有用。

这给了我们下面的结果，这应该更能说明问题(你也可以看到阳光明媚，我一直在偷懒:):

[![Gitlab Time in Lane : a chrome extension](img/0bacc79f8772ba44599b4cd4ea227816.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NTYKNMEU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lengrand.fr/conteimg/2019/06/Screen-Shot-2019-06-09-at-12.03.34-PM.png)

扩展本身非常简单，你只需要[设置一个个人访问令牌](https://gitlab.com/profile/personal_access_tokens)就可以让它工作。

[![Gitlab Time in Lane : a chrome extension](img/3f8b2961108e38a5d1d4cedd16d4270e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r9rlmoo8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lengrand.fr/conteimg/2019/06/Screen-Shot-2019-06-09-at-12.09.11-PM.png)

扩展的主要概念围绕着一个简单的[变异观测器](https://developer.mozilla.org/en/docs/Web/API/MutationObserver)，与 Gitlab API 调用相耦合。chrome 存储中的设置是同步的，因此该扩展将在您的所有设备上同步。UI 是使用 [Vaadin 的 Web 组件库](https://vaadin.com/components)制作的。我尝试使用 lion，它们工作得很好，但是作为一个后端的家伙，我最终选择了一个风格库:)。

让我们暂时保密。你可以试试 **[这里的](https://chrome.google.com/webstore/detail/gitlab-issues-time-in-lan/jecodijiblfkhcgnllajecjpngbngaoi)** 。旋转一下，让我知道你的想法！

你可以在这里找到[源代码](https://gitlab.com/jlengrand/gitlab-issues-time-in-lane)(欢迎帮助)[在这里给出反馈](https://gitlab.com/jlengrand/gitlab-issues-time-in-lane)(或者在 Twitter [@jlengrand](https://twitter.com/jlengrand) )。

很快聊！现在去抱石:)