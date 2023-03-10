# 项目需要帮助/建议

> 原文：<https://dev.to/gac/need-help-suggestions-on-a-project-1pe3>

因此，在过去的几天里，我一直在开发一个简单的网络应用程序，让我兄弟酒吧的服务员可以为客人管理桌子和标签。

我已经做好了一切准备，可以进行 beta 测试了。

但有一件事一直困扰着我，那就是我目前在应用程序中设置条形布局的方式。

我所做的是创建一个`svg <rect>`形状，并硬编码在视图文件* ***叹息*** *中。我们都知道这是一种很糟糕的做法，尤其是当酒吧的布局发生变化的时候。

这是它目前在应用程序中的样子:
[![](img/1b2489e3edcea7f5d5fcf9344428536f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VKlC3mQz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kxoyc9zy2uvesbc4emwb.png)

这是它在代码中的样子:
[![](img/d8657a1e68b3d1080905fb28168c2750.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nclbWR5Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pm891ltsdl7x4iwtpqkf.png)

因此，我想从这个社区听到的是，你将如何尝试这样做，以便我可以允许管理员改变这个布局，如果他们想让它看起来有点不同。也许可以添加一些其他的配置选项。因为目前它看起来不太好，一点也不活跃。我在想也许可以使用`<canvas>`，然后从数据库中存储/加载数据。但是在网上找不到好的教程。

我所能找到的就是如何把画布上的东西存储成 png 文件。

有没有一种方法可以实现这一点，而不必实现复杂的库或在应用程序中嵌入第三方服务？

如果需要，这些是项目规格:
-语言:PHP，jQuery
-数据库:MySQL

这个项目我没有使用任何框架。

感谢任何反馈/帮助。:)