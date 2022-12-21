# 如何设置 Jekyll 博客并链接到 Dev.to

> 原文：<https://dev.to/thefern/how-to-setup-a-jekyll-blog-and-link-to-dev-to-5eap>

我正在写一个系列，虽然我有像 scrivener 这样很棒的写书工具，但它不能很好地与 markdown 配合使用。在本指南中，我将向您展示如何以最少的努力安装 jekyll。

哲基尔-现在[快速启动](https://github.com/barryclark/jekyll-now#quick-start)

*   叉子[哲基尔——现在](https://github.com/barryclark/jekyll-now)
*   在设置下将 repo 重命名为 username.github.io。如果这不是你的主网站，你也可以随便命名。例如，我的主 github.io 被用作文件夹，所以我把我的文件夹重命名为“blog”。Github pages 将自动在 kodaman2.github.io/blog.为其提供服务

如果这是你的 github.io 页面，那么它将可以通过一个演示帖子来查看。如果这不是你的 github.io repo，那么进入设置，向下滚动到 github 页面，选择分支。一旦完成，你应该可以看到你的网站。

## 配置 _ 配置. yml

以下是我更改的设置:名称、描述、头像、github、twitter、url、baseurl、主题。

这个文件有很多注释，所以很容易修改。baseurl 是必要的，因为它不是我的主要 github.io 站点，你可以在评论中看到。如果有问题，请检查更改 [_config.yml](https://github.com/kodaman2/blog/blame/master/_config.yml) 。

网站不需要太长时间就能与回购变更同步。

## 使用 RSS Feed 将帖子发布到开发人员

在[https://dev.to/settings/publishing-from-rss](https://dev.to/settings/publishing-from-rss)输入你网站的 rss 订阅源，如果你使用 github 页面，username.github.io/feed 或 username.github.io/repo_name/feed 点击更新按钮，然后点击顶部的获取按钮。

如果一切正常，您应该会看到“您已经启动并运行”的演示帖子。请注意，文章不会发布，而是作为草稿从 RSS 源同步。

## 发布新帖子

要制作新帖子，请转到 _posts 文件夹，并创建一个新的 md 文件。例如:年-月-日-标题. md。

记得加前面的事，查看演示贴。[前置事项-文档](https://jekyllrb.com/docs/front-matter/)

```
--------
layout: post
title: Blogging Like a Hacker
-------- 
```

Enter fullscreen mode Exit fullscreen mode

希望你喜欢这个快速简单的指南来建立你自己的博客，同时在 dev.to 上发布。感谢阅读。