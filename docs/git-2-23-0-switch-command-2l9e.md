# Git 开关命令

> 原文：<https://dev.to/jetrockets/git-2-23-0-switch-command-2l9e>

新版本的 git
中增加了一个`switch`命令，让我们来看看例子:

```
# switched to <branch>
git switch <branch>

# creates a new <branch>
git switch -c <branch>

# switched to commit 
git switch -d <commit> 

# creates and switches to branch from remote. 
# need to use if branch exists in multiple remotes 
git switch -c <branch> --track <remote>/<branch> 

# switch to a branch even if the index or working tree is different from HEAD
# this is used to throw away local changes
git switch --discard-changes <branch>

# alias for  --discard-changes
git switch -f <branch> 

# switch back to the previous branch before we switched
git switch - 
```

Git 2.23.0 或更高版本上可用的命令