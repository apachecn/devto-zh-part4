# 面向严肃开发者的 API 测试

> 原文：<https://dev.to/gayanhewa/api-testing-for-serious-developers-393i>

好吧，我不是指“严肃的”开发者。我只是想让标题看起来花里胡哨。这对任何人都有效，甚至对那些不在 emacs 生态系统中的人也有效。

我个人花了相当多的时间来构建和使用 web 服务。几年前，我被介绍给邮递员，这让我大吃一惊。因为简单，主要是因为能够在团队中共享集合。到目前为止，我仍然在必要时使用 postman，但我发现很难在我的编辑器和 Postman 客户端之间切换，只是因为我可以测试一个端点或调用我正在集成的一些服务，以便我可以验证其结果。毫无疑问，Postman 对于团队来说是一个很好的工具，尽管它在正确的领域膨胀了。

[![Postman yay!!!](img/4826e82164d4ea8b11a8d64472dd6b9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DQC2Mg1D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4wt8eoafom4cyjclt12j.png)

在切换到 emacs 之后，我在寻找一种方法*不要在开发一个新的 API 或者已经在使用一个 API 的时候*离开编辑器。对于大多数其他 IDE，你可以选择使用插件或者使用带有 [httpie](https://httpie.org/) 的终端会话，并做一些小改动。我不是说这是最好的方法。但在我看来，这无疑是最好的方法。

[![](img/1cf141851eef0d7f035727f7eb7410f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D8STSl1I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i7i9vime7fwtl1fs7phn.png)

### rest client . El 入门

restclient.el 包可以通过 MELPA 轻松安装。这个包提供了一些很棒的文档。代码非常容易理解。

您将有一些默认的键绑定，可以很容易地重新映射，例如，对我来说最常用的是`C-c C-c`在光标下运行测试组。`C-c C-n`跳到下一个查询，而`C-c C-p`跳到上一个查询。另一个巧妙的技巧是，当我想和别人分享一个查询时，我可以通过按下`C-c C-u`快速复制它的 curl 版本

[![](img/3321c55fcb24e39ce789dc8d26a1f82f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xr76Y4ek--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kn393eyg65323hvpy640.png)