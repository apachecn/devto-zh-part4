# 如何使用 Git 管理你的写作项目

> 原文：<https://dev.to/digitalocean/how-to-use-git-to-manage-your-writing-project-2ge8>

### 简介

版本控制不只是针对代码。它适用于任何你想追踪的东西，包括内容。使用 [Git](https://git-scm.com) 来管理你的下一个写作项目，让你能够同时查看多个草稿，看到这些草稿之间的差异，甚至回滚到以前的版本。如果你愿意这样做，你可以在 GitHub 或其他中央 Git 仓库上与他人分享你的工作。

在本教程中，您将使用 Git 来管理一个小的 Markdown 文档。您将存储一个初始版本，提交它，进行更改，查看这些更改之间的差异，并检查以前的版本。完成后，您将拥有一个可以应用于自己的写作项目的工作流程。

## 先决条件

*   Git 安装在您的本地计算机上。教程[如何为开源做贡献:Git 入门](https://www.digitalocean.com/community/tutorials/how-to-contribute-to-open-source-getting-started-with-git)将带您完成 Git 的安装，并涵盖一些您可能会觉得有用的背景信息。

## 第一步——为你的写作项目创建一个工作空间

为了管理您的更改，您将创建一个本地 Git 存储库。Git 存储库位于一个现有的目录中，所以首先为您的文章创建一个新的目录:

```
mkdir article 
```

切换到新的`article`目录:

```
cd article 
```

`git init`命令在当前目录中创建新的空 Git 存储库。现在执行该命令:

```
git init 
```

您将看到以下输出，确认您的存储库已创建:

```
Output
Initialized empty Git repository in /Users/sammy/article/.git/ 
```

`.gitignore`文件让您告诉 Git 应该忽略一些文件。您可以使用它来忽略文本编辑器可能创建的临时文件，或者操作系统文件。例如，在 macOS 上，Finder 应用程序在目录中创建`.DS_Store`文件。创建一个忽略它们的`.gitignore`文件:

```
nano .gitignore 
```

将以下几行添加到文件中:

```
# Ignore Finder files
.DS_store 
```

第一行是注释，这将有助于您识别将来忽略的内容。第二行指定要忽略的文件。

保存文件并退出编辑器。

当您发现更多想要忽略的文件时，打开`.gitignore`文件，为每个想要忽略的文件或目录添加一个新行。

现在您的存储库已经配置好了，您可以开始工作了。

## 第二步——保存你的初稿

Git 只知道你告诉它的文件。仅仅因为一个文件存在于存放存储库的目录中，并不意味着 Git 会跟踪它的变化。您必须向存储库中添加一个文件，然后提交更改。

创建一个名为`article.md`的新降价文件:

```
nano article.md 
```

向文件中添加一些文本:

```
# How To Use Git to Manage Your Writing Project

### Introduction

Version control isn't just for code. It's for anything you want to track, including content. Using Git to manage your next writing project gives you the ability to view multiple drafts at the same time,  see differences between those drafts, and even roll back to a previous version. And if you're comfortable doing so, you can then share your work with others on GitHub or other central git repositories.

In this tutorial you'll use Git to manage a small Markdown document. You'll store an initial version, commit it, make changes, view the difference between those changes, and review the previous version. When you're done, you'll have a workflow you can apply to your own writing projects. 
```

保存更改并退出编辑器。

`git status`命令将显示您的存储库的状态。它将向您显示需要添加哪些文件，以便 Git 可以跟踪它们。运行以下命令:

```
git status 
```

您将看到以下输出:

```
Output
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

    .gitignore
    article.md

nothing added to commit but untracked files present (use "git add" to track) 
```

在输出中，`Untracked files`部分显示了 Git 没有查看的文件。这些文件需要添加到存储库中，这样 Git 就可以观察它们的变化。使用`git add`命令来完成:

```
git add .gitignore
git add article.md 
```

现在运行`git status`来验证这些文件已经被添加:

```
Output
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

    new file:   .gitignore
    new file:   article.md 
```

这两个文件现在都列在`Changes to be committed`部分。Git 知道它们，但是它还没有创建工作的快照。使用`git commit`命令来完成。

当您创建一个新的提交时，您需要提供一个提交消息。一个好的提交消息说明了你的更改是什么。当你和别人一起工作时，你的提交信息越详细越好。

使用命令`git commit`提交您的更改:

```
git commit -m "Add gitignore file and initial version of article" 
```

该命令的输出显示文件已提交:

```
Output
[master (root-commit) 95fed84] Add gitignore file and initial version of article
 2 files changed, 9 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 article.md 
```

使用`git status`命令查看存储库的状态:

```
git status 
```

输出显示没有需要添加或提交的更改。

```
Output
On branch master
nothing to commit, working tree clean 
```

现在让我们看看如何处理变更。

## 步骤 3 —保存修订

您已经添加了文章的初始版本。现在您将添加更多的文本，以便了解如何使用 Git 管理更改。

在编辑器中打开文章:

```
nano article.md 
```

在文件末尾添加更多文本:

```
## Prerequisites

* Git installed on your local computer. The tutorial [How to Contribute to Open Source: Getting Started with Git](https://www.digitalocean.com/community/tutorials/how-to-contribute-to-open-source-getting-started-with-git) walks you through installing Git and covers some background information you may find useful. 
```

保存文件。

使用`git status`命令查看存储库中的情况:

```
git status 
```

输出显示有变化:

```
Output
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   article.md

no changes added to commit (use "git add" and/or "git commit -a") 
```

不出所料，`article.md`文件有变化。

使用`git diff`查看它们是什么:

```
git diff article.md 
```

输出显示了您添加的行:

```
diff --git a/article.md b/article.md
index 77b081c..ef6c301 100644 --- a/article.md +++ b/article.md @@ -5,3 +5,7 @@
 Version control isn't just for code. It's for anything you want to track, including content. Using Git to manage your next writing project gives you the ability to view multiple drafts at the same time,  see differences between those drafts, and even roll back to a previous version. And if you're comfortable doing so, you can then share your work with others on GitHub or other central git repositories.

 In this tutorial you'll use Git to manage a small Markdown document. You'll store an initial version, commit it, make changes, view the difference between those changes, and review the previous version. When you're done, you'll have a workflow you can apply to your own writing projects.
+
+## Prerequisites
+
+* Git installed on your local computer. The tutorial [How to Contribute to Open Source: Getting Started with Git](https://www.digitalocean.com/community/tutorials/how-to-contribute-to-open-source-getting-started-with-git) walks you through installing Git and covers some background information you may find useful. 
```

在输出中，以加号(+)开始的行是您添加的行。被删除的行将以减号(-)显示。没有变化的行前面不会有这两个字符。

使用`git diff`和`git status`是一种很有帮助的方式，可以看到你改变了什么。您还可以将 diff 保存到一个文件中，以便以后使用以下命令查看它:

```
git diff article.md > article_diff.diff 
```

使用`.diff`扩展将帮助您的文本编辑器应用正确的语法高亮显示。

将更改保存到存储库中是一个两步过程。首先，再次添加`article.md`文件，然后提交。Git 希望您明确地告诉它每次提交时放入哪些文件，所以即使您之前添加了该文件，也必须再次添加。请注意，`git status`命令的输出会提醒您这一点。

添加文件，然后提交更改，提供一条提交消息:

```
git add article.md
git commit -m "add prerequisites section" 
```

输出验证了提交工作正常:

```
Output
[master 1fbfc21] add prerequisites section
 1 file changed, 4 insertions(+) 
```

使用`git status`查看您的存储库状态。你会发现没有别的事可做。

```
git status

Output
On branch master
nothing to commit, working tree clean 
```

当你修改你的文章时，继续这个过程。进行更改，验证它们，添加文件，并提交带有详细消息的更改。提交你的改变，只要你觉得舒服就行。你可以在完成每一份草稿之后，或者在对文章结构进行重大修改之前，执行一次提交。

如果您将文档的草稿发送给其他人，而他们对其进行了更改，请拿走他们的副本并用他们的文件替换您的文件。然后使用`git diff`快速查看他们所做的更改。不管你是直接输入还是用从网上、电子邮件或其他地方下载的文件替换文件，Git 都会看到变化。

现在让我们看看如何管理文章的版本。

## 第 4 步—管理变更

有时查看文档的以前版本会有所帮助。每当你使用`git commit`，你就提供了一个有用的信息，总结了你所做的事情。

`git log`命令向您展示了您的存储库的提交历史。您提交的每个更改在日志中都有一个条目。

```
git log

Output
commit 1fbfc2173f3cec0741e0a6b21803fbd0be511bc4
Author: Sammy Shark <sammy@digitalocean>
Date:   Thu Sep 19 16:35:41 2019 -0500

    add prerequisites section

commit 95fed849b0205c49eda994fff91ec03642d59c79
Author: Sammy Shark <sammy@digitalocean>
Date:   Thu Sep 19 16:32:34 2019 -0500

    Add gitignore file and initial version of article 
```

每个提交都有一个特定的标识符。您使用这个数字来引用特定提交的更改。不过，您只需要标识符的前几个字符。`git log --oneline`命令为您提供了一个具有更短标识符的精简版本的日志:

```
git log --oneline 
```

```
Output
1fbfc21 add prerequisites section
95fed84 Add gitignore file and initial version of article 
```

要查看文件的初始版本，使用`git show`和提交标识符。您的存储库中的标识符将与这些示例中的不同。

```
git show 95fed84 article.md 
```

输出显示了提交的详细信息，以及在提交期间发生的更改:

```
Output
commit 95fed849b0205c49eda994fff91ec03642d59c79
Author: Sammy Shark <sammy@digitalocean>
Date:   Thu Sep 19 16:32:34 2019 -0500 
    Add gitignore file and initial version of article

diff --git a/article.md b/article.md
new file mode 100644 index 0000000..77b081c --- /dev/null +++ b/article.md @@ -0,0 +1,7 @@
+# How To Use Git to Manage Your Writing Project
+
+### Introduction
+
+Version control isn't just for code. It's for anything you want to track, including content. Using Git to manage your next writing project gives you the ability to view multiple drafts at the same time,  see differences between those drafts, and even roll back to a previous version. And if you're comfortable doing so, you can then share your work with others on GitHub or other central git repositories.
+
+In this tutorial you'll use Git to manage a small Markdown document. You'll store an initial version, commit it, make changes, view the difference between those changes, and review the previous version. When you're done, you'll have a workflow you can apply to your own writing projects. 
```

要查看文件本身，稍微修改一下命令。提交标识符和文件之间没有空格，而是替换为如下所示的`:./`:

```
git show 95fed84:./article.md 
```

您将看到该文件的内容，在该版本中:

```
Output# How To Use Git to Manage Your Writing Project

### Introduction

Version control isn't just for code. It's for anything you want to track, including content. Using Git to manage your next writing project gives you the ability to view multiple drafts at the same time,  see differences between those drafts, and even roll back to a previous version. And if you're comfortable doing so, you can then share your work with others on GitHub or other central git repositories.

In this tutorial you'll use Git to manage a small Markdown document. You'll store an initial version, commit it, make changes, view the difference between those changes, and review the previous version. When you're done, you'll have a workflow you can apply to your own writing projects. 
```

如果需要做其他事情，可以将输出保存到文件中:

```
git show 95fed84:./article.md > old_article.md 
```

随着你做了更多的修改，你的日志将会增长，你将能够回顾一段时间以来你对文章所做的所有修改。

## 结论

在本教程中，您使用了一个本地 Git 存储库来跟踪您的写作项目中的变更。你可以用这种方法来管理个人文章、博客上的所有文章，甚至是你的下一部小说。如果你把你的库推给 GitHub，你可以邀请其他人帮助你编辑你的作品。