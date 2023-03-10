# 谁能告诉我如何安全地删除 node_modules 来清理硬盘空间？

> 原文：<https://dev.to/sakko/anyone-can-show-me-how-to-safely-delete-nodemodules-to-clear-up-harddisk-space-9kl>

更新时间:

对于任何发现这篇文章的人，
我发现了一个叫做`npkill`的包

[https://github.com/voidcosmos/npkill](https://github.com/voidcosmos/npkill)

-结束-

希尔

我用的是 Mac，它配有 512GB 的固态硬盘。有太多的东西停滞不前，我的固态硬盘空间不足。

我认为 docker 占据了我笔记本电脑的大部分空间。但是我发现清理旧项目会有所帮助。

首先，请让我给你一些背景。

1.  我管理着一个软件开发团队，在我的电脑里有超过 100 个 ruby 和 nodejs 项目。我是这样组织的。
    *   `~/_workspace`所有代码的根文件夹。`_`是为了方便访问。
    *   `~/_workspace/customer_1_name/project_1_name`
    *   `~/_workspace/customer_1_name/project_2_name`
    *   `~/_workspace/customer_2_name/project_1_name`
    *   我通常用下划线来命名文件夹。
2.  我在我的项目中使用`NVM`和`.nvmrc`。
    *   来自`yarn`和`npm i`的`node_modules`文件夹太多，占据了我笔记本电脑的太多空间。
3.  我在我的项目中使用`RVM`、`.ruby-version`和`.ruby-gemset`。
    *   Rails 使用`yarn`,有些项目使用`node_modules`,这也占用了太多空间。
4.  有超过 100 个文件夹需要清理，这是我一个人的事。我的团队也需要这样做。

我需要帮助我安全地删除`node_modules`文件夹。或者如果有人面临同样的问题，请分享你的经验。

非常感谢。