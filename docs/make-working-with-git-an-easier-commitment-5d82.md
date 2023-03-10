# 让使用 Git 变得更容易

> 原文：<https://dev.to/simonwaight/make-working-with-git-an-easier-commitment-5d82>

基础设施即代码(IaC)是云技术领域的一个关键部分，这意味着我已经与一些人进行了多次讨论，这些人传统上不必担心他们作为 IT 操作员的软件开发实践。

一个似乎让很多人感到困惑的关键领域是使用源代码控制的文件版本控制，这个领域的主流技术是 Git。

老实说，如果你试图消化介绍性内容之外的东西，Git 可能看起来有点像野兽！

在这篇文章中，我将看看我认为你需要知道的基础知识，并向你展示一种不需要安装一堆工具就可以开始使用 Git 的快速方法。

### 为什么要去？

很好的问题！🙂

Git 已经成为流行的源代码控制系统选择，但绝不是唯一的选择。您在 Git 中学到的许多概念可以很容易地在其他源代码控制系统中重用。

Git 吸引人们的主要特性(在我看来)是:

*   这是一个分布式(而非集中式)版本控制系统。这意味着您可以在不连接到原始系统的情况下处理文件和使用源代码管理
*   系统上的动作相对来说是轻量级的。一个重要的例子是创建一个分支，该分支使用智能方法来制作源分支的副本，从而减少创建和管理分支的开销(🤓[高级阅读](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell)
*   它被广泛使用，这意味着选择它在您的环境中使用不会使雇佣(或培训)会使用它的人变得困难。

另外，微软已经采用 Git(作为 Azure DevOps 的一部分)作为其源代码控制平台的选择。阅读[Brian Harry 2017 年的博客文章](https://devblogs.microsoft.com/bharry/scaling-git-and-some-back-story/)，这真的很有趣，并且触及了微软对 Git 的贡献，基于使用它对 Windows 的源代码进行源代码控制(300 GB 和 600 万个文件！)

### 重要的 Git 概念和命令

为了使用 Git，您需要理解一些关键的概念和命令。我试着以一种你可以在阅读时建立概念的方式来排列这些。

Repository

The core of Git. A repository contains all your source code and tracks changes to it over time. Think of it is a journaling filesystem with an inbuilt recycling bin for deleted items.

Clone

If you want to work on an existing repository you can clone (or copy) it locally to your machine. When you clone a repository a link is created between your local clone and the *origin* repository. When you run most commands locally you will be interacting with your local copy and not the origin.

Fork

Forking is a concept that originated with GitHub, and strictly speaking isn’t a part of Git itself. When you fork a repository you make a linked copy of it, but it is **not** a clone. You typically fork a repository when you want to contribute content to the repository but don’t have contributor rights on it. A forked repository still has a relationship with the original which means you can submit to, or receive changes from, it in the form of Pull Requests which we cover below (🤓 [advanced reading](https://help.github.com/en/articles/fork-a-repo)).

Branch

Think of a branch as a folder in a filesystem. By default every repository has one branch named *master*. You can create multiple, related branches and name them as you see fit (🤓 [advanced reading](https://docs.microsoft.com/en-us/azure/devops/repos/git/git-branching-guidance?view=azure-devops)). You can even branch locally without ever publishing the branch back to the origin!

Checkout

If you’ve worked with other source control platforms this command might trip you up! When you perform a ‘checkout’ with Git you are, in fact, changing the active branch you are working on and not “checking out” files from Git (🤓 [advanced reading](https://www.atlassian.com/git/tutorials/using-branches/git-checkout)).

Head

This is a reference to the most recent commit in the branch you are working with. The name fairly describes what it is – it’s the head of the branch!

Add

You must perform this action for a file or folder to included in a Repository. Some development software can automatically watch for new files and add them. A file that has not been added to Git is said to be *untracked*.

Commit

When you update existing tracked files or add new files you must commit them to Git for the changes or addition to be source controlled. This is an explicit action you have to take and it is always recommended to provide a commit message that describes what the commit consists of.

Commit hash (ID)

Every commit you make to Git has an ID associated with it. These IDs are not unique as they are an SHA-1 hash of the contents of a commit, but it’s unlikely you’ll find many instances of the same ID in a repository. This ID is usually displayed as an abbreviated set of seven characters similar to ‘acf87c23’ (🤓 [advanced reading](https://blog.thoughtram.io/git/2014/11/18/the-anatomy-of-a-git-commit.html)).

Gitignore (Git ignore)

You create a .gitignore file in your repository to stop certain files or directories from being added – think of things like temporary files or folders or sensitive config files (🤓 [advanced reading](https://git-scm.com/docs/gitignore)).

Pull

When you want to update your cloned repository from its origin you use a pull command to retrieve changes.

Push

When you want to submit your local changes to the origin you push them to the origin.

Merge conflict

This occurs when you perform either pull or push and a file (or files) has been modified in both your local and the origin repository. When this happens Git will try and automatically merge the files for you, but if it can’t then you will need to manually fix. Manual merges and committing merged files can be one of more confusing aspects of working with Git and I’m actually going to recommend [reading existing content from GitHub](https://help.github.com/en/articles/about-merge-conflicts) on this as it will provide you good approaches to deal with conflicts.

Pull Request (or PR)

A Pull Request (PRs) allows you to push changes between branches and between forks. PRs are typically used when you don’t hold the right to contribute on the branch or upstream repository you are submitting to, or in situations where you would like the submitted changes to be reviewed first before being accepted and merged. Pull Requests originated with GitHub and aren’t a part of the core Git platform (much like Forks) (🤓 [advanced reading](https://help.github.com/en/articles/about-pull-requests)).

好了，这就是重要的基线概念！

### 实际入门！

我认为现在最简单的方法之一就是使用 GitHub。如果你还没有，你可以过来[加入社区](https://github.com/join)。

一旦注册，你就可以开始创建存储库并学习如何使用 Git。你可以在这里犯尽可能多的错误，所以发疯吧！🙂

您甚至可以选择派生一个现有的存储库，并探索它是如何结合在一起的。也不需要在您的机器上本地安装任何工具。

一旦你准备好进入下一个阶段，你可以尝试以下方法:

*   **好的第一个问题:**这些被 GitHub 维护者标记为帮助他们开发软件/存储库的好切入点。使用 [GitHub 的搜索](https://github.com/search?q=good+first+issue&type=Issues)来找到他们。
*   **修复微软文档:**这是一种非常酷的使用 Git 的方式。如果你在[文档网站](https://docs.microsoft.com/)发现一个错误，你可以使用 GitHub 驱动工作流程进行编辑。我录制了下面的视频来告诉你如何做到这一点。

请注意，在视频中，我已经登录到我的 GitHub 帐户，我之前已经分叉的微软文档库。

希望您现在已经习惯了使用 Git，并且可以开始在日常工作流程中使用它。

😎