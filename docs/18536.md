# 同步分叉回购

> 原文：<https://dev.to/serhatteker/sync-a-forked-repo-5agd>

更新存储库的分支，使其与上游存储库保持同步。

1.  添加远程(你分叉的原始回购)并调用它`upstream`:

```
 $ git remote add upstream https://github.com/User/original-repo.git 
```

Enter fullscreen mode Exit fullscreen mode

或者使用`SSH`

```
 $ git remote add upstream git@github.com:User/original-repo.git 
```

Enter fullscreen mode Exit fullscreen mode

1.  获取远程`upstream`的所有分支:

```
 $ git fetch upstream 
```

Enter fullscreen mode Exit fullscreen mode

1.  使用 git rebase 用`upstream`的`master`重写你的 master:

```
 $ git rebase upstream/master 
```

Enter fullscreen mode Exit fullscreen mode

1.  将您的更新推送到`master`。

```
 $ git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

您可能需要使用
按下`force`

```
 $ git push origin master --force 
```

Enter fullscreen mode Exit fullscreen mode

全部完成！