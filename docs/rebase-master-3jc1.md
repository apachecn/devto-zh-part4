# Rebase Master

> 原文：<https://dev.to/serhatteker/rebase-master-3jc1>

## Rebase

当你一直在做某件事的时候，可能你一直在断断续续地做某个分支，或者在其他分支发生了很多事情。

最好的解决方案是将您的分支重新设置为 master。这保持了历史的整洁，并使事情更容易跟踪。

从主数据库更新特征分支:

1.  切换`master`支路

```
 $ git checkout master 
```

Enter fullscreen mode Exit fullscreen mode

1.  获取`remote`更新:

```
 $ git pull 
```

Enter fullscreen mode Exit fullscreen mode

1.  切换回您的`local`分支:

```
 $ git checkout local_branch 
```

Enter fullscreen mode Exit fullscreen mode

1.  使`rebase`魔法:

```
 $ git rebase master 
```

Enter fullscreen mode Exit fullscreen mode

1.  将您的`local`分支推到`remote`:

```
 $ git push 
```

Enter fullscreen mode Exit fullscreen mode

如果您已经推动了您的分支，则需要旗帜。

```
 $ git push --force 
```

Enter fullscreen mode Exit fullscreen mode

*** *警告* * :** 做这件事之前要三思而后行。

### 比较树枝

如果你想在“强制推送”之前比较你的`local`和`remote`特征分支:

```
$ git diff <master_branch_path> <remote_branch_path> 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
$ git diff feature origin/feature 
```

Enter fullscreen mode Exit fullscreen mode

其中`feature`是你的`local`分支，`origin/master`是你的`remote`分支。

### Git -你的分支和‘origin/XXX’有分歧

如果您收到以下错误:

```
Your branch and 'origin/xxx' have diverged,
and have 1 and 1 different commit(s) each, respectively. 
```

Enter fullscreen mode Exit fullscreen mode

这很正常。如果您对之前被推送到`origin`存储库的分支重新定基，就会发生这种情况。重置基础重写历史，所以之后你会有不同的`local`和`remote`状态。

我们有这样一段历史:

```
... o ---- o ---- A ---- B  master, origin/master
                   \
                    C  branch_xxx, origin/branch_xxx 
```

Enter fullscreen mode Exit fullscreen mode

而我们就这样“改写”了历史:

```
... o ---- o ---- A ---------------------- B  master, origin/master
                   \                        \
                    C  origin/branch_xxx     C` branch_xxx 
```

Enter fullscreen mode Exit fullscreen mode

解决方法:

```
$ git push origin branch_xxx --force 
```

Enter fullscreen mode Exit fullscreen mode

所以实际状态会像:

```
... o ---- o ---- A ---- B  master, origin/master
                          \
                           C` branch_xxx, origin/branch_xxx 
```

Enter fullscreen mode Exit fullscreen mode

如前所述:

*** *警告* * :** 做这件事之前要三思而后行。

* * *

有关更多详细信息:

[git-scm — Git 分支-重置基础](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)