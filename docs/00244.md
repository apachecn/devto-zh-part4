# 我的 peco 笔记

> 原文：<https://dev.to/n350071/my-peco-note-aha>

```
$ cat .bash_profile
alias cdp='cd $(ls -al | grep ^d | tr -s " " | cut -d" " -f9 | peco); pwd;' 
```

Enter fullscreen mode Exit fullscreen mode

```
cat ~/.gitconfig
[user]
    name = 
    email = 

[alias]
  check = !git checkout $(git branch | sed 's/*//g' | sed 's/ //g' | peco)
  bcp  = !git branch | peco | sed 's/*//g' | sed 's/ //g' | tr -d '\n'  | pbcopy
[log]
    date = iso-local
[core]
    quotepath = false
[commit]
    template = /Users/naoki/.git_commit_template
[filter "lfs"]
    clean = git-lfs clean -- %f
    smudge = git-lfs smudge -- %f
    process = git-lfs filter-process
    required = true
[init]
    defaultBranch = master 
```

Enter fullscreen mode Exit fullscreen mode