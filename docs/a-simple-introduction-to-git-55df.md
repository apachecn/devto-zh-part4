# git 的简单介绍

> 原文：<https://dev.to/dagasatvik10/a-simple-introduction-to-git-55df>

版本控制系统(VCS)是开发人员需要了解的最重要的事情之一。自 2005 年发布以来，git 一直是使用最广泛的 VCS 之一。因此，不再拖延，让我们从 git 的基础开始吧。

首先要做的是在你使用的任何操作系统上安装 git。我们来看看在 Ubuntu 上是怎么做的。

```
$  sudo apt-add-repository ppa:git-core/ppa
$  sudo apt-get update
$  sudo apt-get install -y git 
```

Enter fullscreen mode Exit fullscreen mode

检查您的系统中是否安装了 git。您应该会看到类似这样的内容。

```
$  git --version
git version 2.20.1 (Apple Git-117) 
```

Enter fullscreen mode Exit fullscreen mode

**太好了！**让我们进行第一次提交。

创建一个新的项目文件夹，并将其初始化为 git 存储库。

```
$  mkdir git-tutorial
$  cd git-tutorial
$  git init
Initialized empty Git repository in /home/sa.daga/src/demo/git-tutorial/.git/ 
```

Enter fullscreen mode Exit fullscreen mode

git 中有 3 个不同的代码区域:

*   未分级
*   阶段
*   坚定的

在 git-tutorial 目录中的终端上运行以下命令。

```
$  git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track) 
```

Enter fullscreen mode Exit fullscreen mode

命令告诉你哪些代码是暂存的，哪些是未暂存的。

在 git-tutorial 目录中创建一个新文件，并再次运行`git status`。

```
$  echo "test1" > a.txt
$  cat a.txt
test1 $  git status
On branch master

No commits yet

Untracked files:
 (use "git add <file>..." to include in what will be committed)  a.txt

nothing added to commit but untracked files present (use "git add" to track) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，它显示您的目录中有一个名为 **a.txt** 的文件，并且它是未被跟踪的。Untracked 是 git 的说法，表示目前它不关心这个文件中的更改。 **a.txt** 中的变化目前在未登台区。

注意:不要混淆未跟踪和未分级。文件未被跟踪，但更改未被暂存。

现在将该文件添加到暂存区域，并再次运行`git status`。

```
$  git add a.txt
$  git status
On branch master

No commits yet

Changes to be committed:
 (use "git rm --cached <file>..." to unstage)  new file:   a.txt 
```

Enter fullscreen mode Exit fullscreen mode

**a.txt** 现已移至舞台区。
让我们修改 **a.txt** 并再次运行`git status`。

```
$  echo "test2" >> a.txt
$  cat a.txt
test1
test2 $  git status
On branch master

No commits yet

Changes to be committed:
 (use "git rm --cached <file>..." to unstage)  new file:   a.txt

Changes not staged for commit: (use "git add <file>..." to update what will be committed)
 (use "git checkout -- <file>..." to discard changes in working directory)
 modified:   a.txt 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，`git status`告诉我们 **a.txt** 已经被修改。所以包含 *test1* 的 **a.txt** 在登台区，而变更 *test2* 在未登台区。

*注意:git 命令的输出可以包含关于下一步可以运行什么命令的信息。与上面命令的输出一样，git 显示了一些命令，如果您想要取消暂存一个已暂存的更改，将未暂存的更改添加到已暂存的区域或者放弃文件中的更改，您可以运行这些命令。*

您甚至可以通过运行`git diff`来查看每个文件中的修改。

```
$  git diff
diff --git a/a.txt b/a.txt
index 9daeafb..8e042fb 100644
--- a/a.txt
+++ b/a.txt
@@ -1 +1,2 @@
 test1
+test2 
```

Enter fullscreen mode Exit fullscreen mode

`git diff`的输出显示 *test2* 已经被追加到 *test1* 的下一行 **a.txt** 。
让我们添加您的第一个提交。仅提交分阶段的更改。

```
$  git commit -m "some commit message"
[master (root-commit) 4d75ac6] some commit message
 1 file changed, 1 insertion(+)
 create mode 100644 a.txt 
```

Enter fullscreen mode Exit fullscreen mode

`git commit`命令用于添加一个提交。选项 **-m** 用于将提交消息传递给提交，该消息描述了正在提交的更改。用一个定制的消息替换*一些提交消息*，该消息描述了正在提交的变更。

*注意:提交消息是添加提交所必需的。如果 **-m** 选项没有和* `git commit` *一起传递，一个编辑器(通常是 vim 或 nano)打开，你必须在那里写一个提交消息。*

再次运行`git status`，然后运行`git diff`。

```
$  git status
On branch master
Changes not staged for commit:
 (use "git add <file>..." to update what will be committed)
 (use "git checkout -- <file>..." to discard changes in working directory)
 modified:   a.txt

no changes added to commit (use "git add" and/or "git commit -a") $  git diff
diff --git a/a.txt b/a.txt
index 9daeafb..8e042fb 100644
--- a/a.txt
+++ b/a.txt
@@ -1 +1,2 @@
 test
+git 
```

Enter fullscreen mode Exit fullscreen mode

`git status`不再显示*尚未提交*消息。此外，由于已提交阶段性变更，因此`git status`中仅显示未阶段性变更。
`git diff`显示每个文件中与阶段性变更相关的变更，如果它们存在的话；否则显示与提交的变更相关的变更。

要查看提交的历史，您可以运行`git log`。

```
$  git log
commit 4d75ac6ce5c533b2ec7bac5e0e00e112b2d5d417 (HEAD ->  master)
Author: dagasatvik10 <dagasatvik10@gmail.com>  Date:   Wed Jul 24 02:13:23 2019 +0530

    some commit message 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，如果没有参数，`git log`会按时间倒序列出项目中的提交；也就是说，最近的提交首先显示。

## 总结起来

恭喜你完成了本教程！其中，我们介绍了基本的 git 命令，如`git init`、`git status`、`git add`、`git diff`、`git commit`和`git log`。继续探索关于 git 的更多信息，如果您有任何问题，请随时提问。

这是我的第一篇博客，所以请给我反馈，告诉我如何改进。