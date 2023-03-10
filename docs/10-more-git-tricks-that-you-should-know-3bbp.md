# 你应该知道的另外 10 个 Git 技巧

> 原文：<https://dev.to/jacobherrington/10-more-git-tricks-that-you-should-know-3bbp>

<sup>封面图片:[两个人拿着 Macbook Pro](https://www.pexels.com/photo/two-people-holding-macbook-pro-1181275/)by[Christina Morillo](https://www.pexels.com/@divinetechygirl)</sup>
我最近写了一篇文章，叫做 [10 个 Git 招数来节省你的时间和理智](https://dev.to/jacobherrington/10-git-tricks-to-save-your-time-and-sanity-289h)。人们似乎很喜欢它，所以这里有更多的 git 技巧，它们作为别名保存在我的机器上。

### 1。获取存储库中最早提交的日期

有时候，当我想知道一个开源项目到底有多老了，或者我想知道回购是什么时候进行的，我会让 git 告诉我第一次提交是什么时候。

```
git log --date-order --format=%cI | tail -1 
```

Enter fullscreen mode Exit fullscreen mode

### 2。查看每个人上个月提交的数量

这是我通常不需要的东西，但偶尔我会被一个项目卡住，需要弄清楚我应该向谁寻求帮助。

对我来说，最简单的方法就是找出谁最近在这个存储库上工作。使用这个命令，您可以将`--since='1 month ago'`切换到您认为最有意义的时间范围。

```
git shortlog --summary --since='1 month ago' 
```

Enter fullscreen mode Exit fullscreen mode

### 3。将所有未跟踪的文件添加到。gitignore

有时候，当你建立一个新的 git 库时，将一堆东西转储到`.gitignore`文件夹中是有意义的，并且键入所有的内容可能是乏味的。

我在网上找到的这个片段将把 git 当前没有跟踪的所有内容添加到。gitignore 文件。

```
git status 
| grep -P \"^\\t\" 
| grep -vF .gitignore 
| sed \"s/^\\t//\" >> .gitignore" 
```

Enter fullscreen mode Exit fullscreen mode

### 4。显示所有忽略的文件

接下来最明显的事情就是列出那些被忽略的文件。

这正是下一个代码片段要做的:

```
git ls-files --others -i --exclude-standard 
```

Enter fullscreen mode Exit fullscreen mode

### 5。列出所有未合并的分支

这个代码片段列出了尚未合并的每个分支。如果您不想在遥控器上包含分支，可以移除`-a`标志。

我在 [Solidus](https://solidus.io) extensions 上使用这个来确保周围没有任何包含废弃工作的额外分支。

```
git checkout master && git branch -a --no-merged 
```

Enter fullscreen mode Exit fullscreen mode

### 6。列出对特定文件的所有更改，即使它已被重命名

这是一个超级方便的技巧。使用这个命令，您可以看到所有涉及文件的提交。

因为 git 很棒，它会向您显示对该文件的更改，即使在其历史的某个点上名称已经被更改。

```
git log --follow -p -- <file_path> 
```

Enter fullscreen mode Exit fullscreen mode

### 7。删除已合并到主服务器的本地分支

如果您发现您的机器上有许多不再需要的随机分支，您可以通过删除它们来实践良好的 git 卫生。

该代码片段将检查是否有任何分支已经被合并到 master 中，并为您自动删除它们。

小心这一个，如果你害怕删除旧的分支。

```
git checkout master 
&& git branch --merged master 
| grep -v "master" 
| xargs -n 1 git branch -d 
```

Enter fullscreen mode Exit fullscreen mode

### 8。显示每个本地分支上次更改的时间

在项目的生命周期中，一些分支可能会被放弃。

下面的代码片段是一种快速判断本地是否有很长时间没有更新的分支的方法。

```
git for-each-ref --sort=committerdate refs/heads/ 
--format='%(HEAD) %(color:yellow)%(refname:short)%(color:reset) - (%(color:green)%(committerdate:relative)%(color:reset))' 
```

Enter fullscreen mode Exit fullscreen mode

### 9。将文件夹旋转成新的回购

如果您的项目足够复杂，您可以使用此命令在当前存储库中的目录外创建一个新的 repo。

运行这个命令后，您可能希望将您的更改推送到新存储库的远程位置。

```
git filter-branch --prune-empty --subdirectory-filter <folder_name> master 
```

Enter fullscreen mode Exit fullscreen mode

### 10。在你最后一次提交时添加一个合著者

当多人参与提交时，GitHub 和 GitLab 支持在提交中添加合作者。

这样做应该将 GitHub 上的提交链接到多个作者。

```
OLD_MSG=$(git log --format=%B -n1) 
&& git commit --amend -m "$OLD_MSG" 
-m "Co-authored-by: jacobherrington <jacobherringtondeveloper@gmail.com>" 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！你应该知道的另外 10 个 Git 技巧🤠

对于我在评论中分享的东西，我很乐意听到更多整洁的 git 技巧或替代方法！👇