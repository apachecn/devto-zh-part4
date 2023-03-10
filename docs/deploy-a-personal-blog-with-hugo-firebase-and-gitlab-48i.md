# 使用 Hugo、Firebase 和 Gitlab 部署个人博客

> 原文：<https://dev.to/rhanarion/deploy-a-personal-blog-with-hugo-firebase-and-gitlab-48i>

# 博客需求

我最近评估了在互联网上写内容和开辟个人空间对我来说会是什么样子，并提出了一系列要求:

*   拥有我的内容(没有第三方平台作为唯一的分发方式。媒介出现之前有一段时间，媒介出现之后也会有一段时间。说到这里，如果你想看结果“在行动中”你可以在我的博客上找到这篇文章:[https://rhazn . com/posts/deploy-a-personal-blog-with-Hugo-firebase-and-git lab/](https://rhazn.com/posts/deploy-a-personal-blog-with-hugo-firebase-and-gitlab/)；))
*   易于设置和维护(我不想设置和托管数据库或复杂的 CMS 系统，当我一个月后回来时，它应该很容易更新。)
*   长期安全存储(不通过第三方托管我的内容，而是在 git 中，我可以很容易地备份它，保留旧的版本，并且不用担心托管提供商关闭。)

所有这些需求都可以通过使用静态站点生成器并在 git 中保持原始文件的版本来解决。当建立一些持续集成时(例如用 gitlab ),它应该相当容易长期维护。

我选择 gohugo 作为站点生成器是因为 Fabian Gruber 在这里写了一篇非常好的设置文章:[https://www.fabiangruber.de/posts/setup-and-deployment](https://www.fabiangruber.de/posts/setup-and-deployment)

# 自动化构建

我使用下面的 gitlab-ci.yml 建立了一个使用 gitlab 的自动化构建: