# Git-自动拉生产服务器？

> 原文：<https://dev.to/ian/git-automatic-pull-on-production-server-3en2>

嗨，

我在托管自己的 Gitea 服务器，我的大部分 web 项目都存储在上面。

在将变更推送到回购之后，我总是需要连接到我的共享托管服务器，在那里我克隆了回购并运行“git pull”。

我知道我可以设置一个 cronjob 来每隔几分钟运行一次这个命令，但是有没有更好的解决方案，允许即时部署呢？

谢谢！