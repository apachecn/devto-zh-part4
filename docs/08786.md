# 同步两个 git 回购(通过使用下面的命令，您可以将一个 Git 回购的内容移动到另一个，而不会丢失历史记录和标签)

> 原文：<https://dev.to/sudhakar6/syncing-two-git-repos-1lhh>

**克隆 SourceRepo 并将其推送到 TargetRepo**

*git 克隆镜像源重置 URL*

上面的 git clone 命令将在您的本地机器上创建一个目录，并以您的 repo 命名。

转到该目录，并向目标存储库添加一个远程设备。

*CD source repo
git REMOTE add NEW-REMOTE TargetRepoURL*T3】它创建一个与 TargetRepo 的连接。
这里的‘新-远程’只是一个名字，你可以给出自己的名字。记住这个名字来同步这两个回购。

*git push NEW-REMOTE-mirror*
它会将源回购的所有文件夹和历史推送到目标回购。

**同步这两个回购**

在克隆源并推送到目标之后，如果您对源做了任何更改，只需在同一个目录中使用以下两个命令进行同步。

*git 取原点*
拉 SourceRepo 修改

*git push NEW-REMOTE-all*
将 SourceRepo 更改推(同步)到 TargetRepo。