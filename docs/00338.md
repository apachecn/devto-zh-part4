# 2019 年的 Wordpress 工作流程

> 原文：<https://dev.to/tranminhdz/wordpress-workflow-in-2019-c2h>

嗨，

我是 Wordpress 的新手，我通过 Udemy 课程学习 Wordpress，并阅读了一些不是最新的文章。

**目前我正在使用**

*   No git
*   由飞轮本地创建本地 WP 项目
*   所有在一个 wp 迁移部署(它将覆盖所有的东西)
*   WP 迁移数据库以更新数据库
*   Ftp 或 Wordpress 文件管理器插件来更新 WP 内容

然而，我意识到这个堆栈已经很旧了，我必须手工做大量的工作。我想问问大家关于 2019 年的 wordpress 工作流

**工作流程**

**1。Git:**
我对 Git 很熟悉，但不确定如何在 WP 中使用。

*   我应该在项目的根目录下初始化 git 并使用 [WP gitignore](https://github.com/github/gitignore/blob/master/WordPress.gitignore) 还是在 wp-content 目录下初始化 git？
*   我应该忽略插件吗

我尝试在项目的根目录下初始化 git 并添加 gitignore，但是 git 跟踪了许多奇怪的文件，我不确定是否应该忽略它们

**2。Webpack**
我熟悉 Webpack，因为我曾在 React 工作过。我可以很容易地设置 Webpack 和 Babel 来捆绑我的项目(我在自定义主题中设置了它)。然而，我不知道如何优化可湿性粉剂图像。我看到 WP 有很多插件， [wp-smushit](https://wordpress.org/plugins/wp-smushit/) ，对于这一点，我应该使用它们还是有更好的选择。既然我看到有很多 gulp 教程，我应该使用 webpack 而不是 gulp 吗？

**3。上传可湿性粉剂内容到舞台和生产**
目前我使用 Ftp，但我知道这是老方法。我必须记住哪些文件已经改变，然后上传它们。我如何自动上传这些更改的文件(当我合并到开发分支进行暂存和主分支进行生产时)？这是否取决于主机服务，因为我可以看到价格非常昂贵，...)

**4。由于生产数据库不断变化(我打算建立 Woocommerce web)，我不能在一次迁移中使用 WP migrate pro 或 All，因为它将覆盖整个数据库。**

目前，在上传了 wp-content 到 staging 和 production 之后，我必须进入 production 并手动添加页面、高级自定义字段。我可以看到高级自定义字段有导出/导入工具，我应该使用该工具将新的高级自定义字段推向生产吗？

如果我在本地添加一个页面，或者添加一个高级自定义字段。我如何将它推向生产和准备阶段？

**5。同步生产数据库，stagingDB 与本地**
为了让我的本地与生产同步，我使用 WP migrate pro 来实现，因为覆盖本地数据库没有坏处

**6。wp-sync-db 和 wp-migrate-pro 有什么区别？**
非常感谢各位。你能建议你的开发工作流程吗？