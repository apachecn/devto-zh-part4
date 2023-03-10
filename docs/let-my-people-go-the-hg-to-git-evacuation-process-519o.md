# 让我的人走:从 Hg 到 Git 的撤离过程。

> 原文：<https://dev.to/nthmost/let-my-people-go-the-hg-to-git-evacuation-process-519o>

当 bitbucket 最近宣布 mercurial support 即将在 2020 年 5 月遭遇厄运时，我知道我的时间到了。我的[各种](https://pypi.org/project/sensate) [项目](https://pypi.org/project/illuminate)和[开放](https://pypi.org/project/metapub) [来源](https://pypi.org/project/metavariant) [投稿](https://pypi.org/project/lubdub)再也受不了比特桶了。

是时候考虑如何将我的 mercurial 库导出到 git 了。

(TL；DR-fast-export 对我有效，但有一个问题。见下文。)

一段时间以来，我一直在观察 bitbucket.org 在推动开源软件建设方面如何无法与 github.com 相提并论。似乎我一直在等待那个众所周知的“踢裤子”离开我的状语屁股，并带领我的乌合之众的回复离开 bitbucket 的功能沙漠，进入 github 的开源乐土。

(现在，我*也*将有各种 CI/CD 风格的作品放在我的 READMEs 上！)

我的拖延症都集中在从 mercurial 到 git 的过渡上。我在 git 方面并不缺乏经验，在 2019 年，我们早已不再模糊 mercurial 或 git 是否会成为首选的源代码控制崇拜。

然而，不祥的乌云在我的存储库历史中盘旋，从一个系统到下一个系统。

因为当我寻找有助于这个过程的工具时，我只找到了两个:

*   **[快速导出](https://github.com/frej/fast-export)**
*   [hg 去](https://hg-git.github.io/)

我很快试镜了这两个角色，结果都失败了。
啊！伟大而强大的开源软件啊，为什么你要如此折磨我？

由于最终我没有让 hg-git 工作，这篇文章将简单地详述我让**快速导出**执行正确仪式的步骤，以及我遇到的主要问题和我如何解决它。

[https://dzone . com/articles/convert-a-mercurial-repository-to-git-using-Hg-Fas](https://dzone.com/articles/convert-a-mercurial-repository-to-git-using-hg-fas)

## 逻辑步骤

**或者说，我们想要实现的目标:**

*   创建一个新的空 git repo 作为导出的目标容器。
*   在本地，将您的 mercurial repo 设置为 git repo。设置设置，以确保 git 忽略汞臭气。
*   将 repo 配置为将步骤 1 中新创建的 git repo 作为其远程。
*   使用快速导出功能导出汞历史记录。
*   git 提交和 git 推送—设置—上游原始主机
*   检查 github 上的新回购，以确保一切都在那里。

## 实用步骤

或者说，我们实际要做的事情。

首先，您应该*在您正在使用的任何服务上创建一个新的空 git repo* 。确保你手边有**遥控器**的信息，以备后用。

现在，我将采取不同寻常的步骤，从中间出现的问题开始，这样指令在整体上更有意义。

运行 fast-export.sh 脚本时，许多人会遇到以下无用的错误消息:

```
 Traceback (most recent call last):
   File "/home/nthmost/fast-export-190107/hg-fast-export.py", line 7, in <module>
     from mercurial.scmutil import revsymbol
 ImportError: cannot import name revsymbol 
```

这意味着它肯定没有进行导出。我们做什么呢

事实证明，在过去一年多的时间里，快速导出工具的几个版本都不可靠。通过[对问题](https://github.com/frej/fast-export/issues/132)的探究，我发现唯一可靠的最新版本被标记为“v180317”。

所以你应该做的是:

*   `git clone https://github.com/frej/fast-export.git`
*   `cd fast-export`
*   `git checkout tags/v180317`

一旦您有了这个特定版本的快速导出，现在您可以继续下面的步骤:

*   `cd /your/target/repo`
*   `git init`
*   `../fast-export/hg-fast-export.sh -r /your/hg/repo`
*   `git remote add origin https://github.com/user/repo.git`
*   `git commit`
*   `git push —set-upstream origin master`

请注意，直接在您开始使用的 mercurial 目录中完成所有 git 工作是完全合适的。如果是这样，你可以用一个`.`代替`fast-export`命令的`/your/hg/repo`。

如果您想继续在 git 中使用 mercurial，那么您应该让 hg-git 工作起来；否则，我建议只有在你从 git repo 重新克隆并清除了你以前 mercurial 生活的所有痕迹之后，才继续开发这个项目。🙂

仅此而已！我希望这也许能让你省下几分钟敲键盘的时间。保持信念！