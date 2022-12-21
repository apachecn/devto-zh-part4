# 从堆栈溢出问题中选择 Post，其中 Topic = " git " ORDER BY Votes desc；

> 原文：<https://dev.to/stackoverflow/select-post-from-stack-overflow-questions-where-topic-git-order-by-votes-desc-id1>

最近，我的一个朋友，杰米·霍尔写了一个非常棒的实用教程，名为[“擅长 git”](https://jamiehall.cc/2019/08/17/get-good-at-git/)。在书中，他介绍了使用每个人都“喜欢被讨厌”的版本控制系统所需要知道的基本工作流(他的笑话，不是我的)。这不仅是对 git 的一个可爱的介绍，这一段真的引起了我的共鸣:

> 实际上，每个人都会不断地搞砸。我说的是拥有多年经验的专业软件工程师，他们通过非受迫性错误和愚蠢的错误完全[搞砸]了他们的工作。

git 的问题如此普遍，以至于凯蒂·塞勒-米勒 [创建了一个网站](https://ohshitgit.com/)，用简单的英语解释如何摆脱 git 的困境。

我知道在栈溢出中能找到让其他完全搞砸事情的开发者放心的公司的最好地方，所以我决定看看[有史以来最热门的投票问题](https://data.stackexchange.com/stackoverflow/query/36657/most-upvoted-questions)来看看 git 问题有多普遍。结果是**投票最多的 10 个问题中有 5 个是关于获得 git** 帮助的。

如果你遇到了这些问题中的一个(我知道我遇到过)，你并不孤单！我使用了 [DEV 的新堆栈溢出液体标签](https://dev.to/devteam/changelog-stack-overflow-liquid-tag-12o2)来嵌入关于堆栈溢出的前所未有的 git 问题。另外，在栈溢出时，你现在也可以[点击“分享”并在任何帖子上选择“开发”](https://dev.to/devteam/native-share-to-dev-button-is-now-on-stack-overflow-3a5a)。

## #2 最高票数问题

<header>

# 如何在 Git 中撤销最近的本地提交？

May 29 '09 Comments: 14 Answers: 98[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)24190![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/927358/how-do-i-undo-the-most-recent-local-commits-in-git) </header>

我不小心**将错误的文件**提交给了 [Git](https://en.wikipedia.org/wiki/Git) ，但是还没有将提交推送到服务器。

我如何从本地存储库撤销这些提交？

唯一的方法似乎是在某种 GUI 文本编辑器中复制编辑内容，然后擦除整个本地…

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/927358/how-do-i-undo-the-most-recent-local-commits-in-git)</button>

## #3 最高票数问题

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [如何本地和远程删除 Git 分支？](https://stackoverflow.com/questions/2003505/how-do-i-delete-a-git-branch-locally-and-remotely)

Jan 5 '10 Comments: 11 Answers: 41[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)19122![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/2003505/how-do-i-delete-a-git-branch-locally-and-remotely) </header>

我想在本地和远程删除一个分支。

### 尝试删除远程分支失败

```
$ git branch -d remotes/origin/bugfix
error: branch 'remotes/origin/bugfix' not found
$ git branch -d origin/bugfix
error: branch 'origin/bugfix' not found.

$ git branch -rd origin/bugfix
Deleted remote branch origin/bugfix (was 2a14ef7).

$ git
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/2003505/how-do-i-delete-a-git-branch-locally-and-remotely)</button>

## #4 最高票数问题

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png)[‘git pull’和‘git fetch’有什么区别？](https://stackoverflow.com/questions/292357/what-is-the-difference-between-git-pull-and-git-fetch)

Nov 15 '08 Comments: 6 Answers: 38[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)13136![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/292357/what-is-the-difference-between-git-pull-and-git-fetch) </header>

`git pull`和`git fetch`有什么区别？

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/292357/what-is-the-difference-between-git-pull-and-git-fetch)</button>

## #8 最高票数问题

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [如何在提交前撤销‘git add’？](https://stackoverflow.com/questions/348170/how-do-i-undo-git-add-before-commit)

Dec 7 '08 Comments: 9 Answers: 37[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)10448![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/348170/how-do-i-undo-git-add-before-commit) </header>

我错误地使用以下命令向 Git 添加了文件:

```
git add myfile.txt 
```

我还没有跑过`git commit`。有没有办法撤销这个操作，这样这些文件就不会包含在提交中了？

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/348170/how-do-i-undo-git-add-before-commit)</button>

## #9 最高票数问题

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [如何重命名本地 Git 分支？](https://stackoverflow.com/questions/6591213/how-do-i-rename-a-local-git-branch)

Jul 6 '11 Comments: Answers: 36[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)10369![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/6591213/how-do-i-rename-a-local-git-branch) </header>

我不想重命名远程分支，如 *[重命名本地和远程 Git 库的主分支](https://stackoverflow.com/questions/1526794/rename-master-branch-for-both-local-and-remote-git-repositories?answertab=votes#tab-top)* 中所述。

我如何重命名一个没有被推送到远程存储库的本地分支？

万一你需要重命名一个**远程**分支:
*[如何做](https://stackoverflow.com/questions/30590083/how-to-rename-a-remote-git-branch-name/30590238#30590238)* …

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/6591213/how-do-i-rename-a-local-git-branch)</button>

## 你呢？

有没有关于 git 的堆栈溢出问题是你经常引用的？你最大的困惑是什么？你有没有和 git 有过“哦，废话”的时候？