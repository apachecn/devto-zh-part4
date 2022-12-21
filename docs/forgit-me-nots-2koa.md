# 别忘了我

> 原文：<https://dev.to/vnglst/forgit-me-nots-2koa>

## 在 Windows 上挤压提交

我知道有一个更好的方法来做到这一点(使用 rebase)，但我喜欢这种方式，因为它给了我更多的控制。

```
git merge-base master yourBranch

# note commit hash

git reset --soft # enter commit hash

# all changes can now be committed again 
```

Enter fullscreen mode Exit fullscreen mode

在*nix 上使用下面的 oneliner 更容易:

```
git reset $(git merge-base master $(git rev-parse --abbrev-ref HEAD)) 
```

Enter fullscreen mode Exit fullscreen mode

## 不拉师傅就换基

使用远程主控器重设基础，以确保您真正了解最新情况。更新合并请求时有用(在 GitLab 上)。

```
git pull --rebase origin master 
```

Enter fullscreen mode Exit fullscreen mode

## 将文件从主文件复制到当前分支

```
git checkout master -- yarn.lock 
```

Enter fullscreen mode Exit fullscreen mode

## 有用的别名

### 全部相加并提交

```
ac=!git add . && git commit 
```

Enter fullscreen mode Exit fullscreen mode

### 漂亮日志

```
ls = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit 
```

Enter fullscreen mode Exit fullscreen mode

### 用当前更改更新上次提交

```
amen = !git add --all && git commit --amend 
```

Enter fullscreen mode Exit fullscreen mode

### 撤销上次提交

```
git reset --soft HEAD^ 
```

Enter fullscreen mode Exit fullscreen mode

或者添加为别名:

```
git config --global alias.undo 'reset --soft HEAD^' 
```

Enter fullscreen mode Exit fullscreen mode

## 哦不，我把我的作品都删了！？

使用以下命令:

```
git reflog 
```

Enter fullscreen mode Exit fullscreen mode

并且`cherry-pick`任何你认为仍然可以拥有你所有工作的提交。

## 如果。gitignore 不起作用

做:

```
git rm -r --cached . 
```

Enter fullscreen mode Exit fullscreen mode

在那之后:

```
git add --all 
```

Enter fullscreen mode Exit fullscreen mode

## 重设基数后

开发人员应该使用
，而不是使用-f 或 force

```
git push --force-with-lease 
```

Enter fullscreen mode Exit fullscreen mode

为什么？因为它检查远程分支的更改，这绝对是一个好主意。让我们假设 James 和 Lisa 正在处理同一个特性分支，Lisa 已经提交了一个提交。詹姆斯现在重建了他在当地的分支机构，但在试图推动时遭到拒绝。当然，詹姆斯认为这是由于 rebase 和 uses-force 造成的，他会重写 Lisa 的所有修改。如果 James 使用了-force-with-lease，他将会收到一个警告，提示有其他人进行了提交。我不明白为什么有人会在追求改变基础时使用武力而不是武力租赁。

来源:[https://stack overflow . com/questions/8939977/git-push-rejected-after-feature-branch-rebase](https://stackoverflow.com/questions/8939977/git-push-rejected-after-feature-branch-rebase)