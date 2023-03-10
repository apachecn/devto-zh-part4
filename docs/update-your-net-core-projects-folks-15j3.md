# 更新您的。网核心项目，各位！

> 原文：<https://dev.to/dotnetcoreblog/update-your-net-core-projects-folks-15j3>

*这篇文章的标题图片取自的官方支持政策。网芯 T3】*

终于到那个时候了！是:[。NET Core 1.0 和 1.1 已经离开 LTS，不再接受微软的服务支持。](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)

### 什么意思？

嗯，你应该已经把你的项目升级到(至少)了。NET Core 2.1(虽然首选 2.2)。也就是说你不需要看这篇文章。

耶！

但是对于那些还没有升级的人来说:现在是时候升级了。说真的，和你的项目经理/项目所有者/客户/任何人谈谈，告诉他们你*需要*花时间升级到支持的版本。网芯。

如果他们不想给你时间，你该怎么做取决于你自己。但就个人而言，我不想支持一个针对不支持的框架编写的应用程序堆栈。

对于一些人来说，升级的途径非常简单:编辑 csproj，将目标框架名字[的版本升级到 2.1 或 2.2，点击 build，瞧！](https://docs.microsoft.com/en-us/dotnet/standard/frameworks)

*感谢[保罗·希尔](https://codeshare.co.uk/)在这里指出我的错别字🎻*

对其他人来说，这可能会有点痛苦，可能意味着要改变一些事情。

无论哪种方式，都值得看一看文档中的 [1.x 到 2.x 迁移指南](https://docs.microsoft.com/en-us/aspnet/core/migration/1x-to-2x/?view=aspnetcore-1.0)。

此外，了解支持日期是为了什么也是值得的。网芯。这样，您就可以围绕项目的迁移进行规划，以确保它们得到支持。