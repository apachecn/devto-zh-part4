# 想更早见面的 git command 们

> 原文：<https://dev.to/seattleconsulting/git-command-26cm>

# 前言

所谓的 n 号煎
列出了业务中使用的指令中特别喜欢的东西。

适合初学者还是中级初学者？ 我觉得是。

我觉得记住命令会让开发变得更有趣。
那样的事情也能做，能做的事情会增加，真是太棒了。

## 方便命令

### 将差分追加到临时区域

```
git add -p 
```

Enter fullscreen mode Exit fullscreen mode

有用度:★★★★★
基本上，只能通过该选项进行添加。

### 取消差分

```
git checkout {FILE_NAME}
git checkout # 全部なかったことに。
git checkout -p # もある 
```

Enter fullscreen mode Exit fullscreen mode

有用度:★☆☆☆☆
虽然有时会用，但我觉得很危险。

### 从临时区域返回(在 add 之前返回)

```
git reset {FILE_NAME}
git reset # 全部戻す
git reset -p # もある 
```

Enter fullscreen mode Exit fullscreen mode

有用度:★★★☆☆
在准备提交的时候，“那为什么这家伙？ ”时使用的
在提交前一定要做`git status`。

### 查看指定用户的提交日志

```
git log --committer={USER_NAME} 
```

Enter fullscreen mode Exit fullscreen mode

重要度:★★☆☆☆
发现可疑的人时。

### 指定提交 ID，确认内容

```
git show {COMMIT_ID} 
```

Enter fullscreen mode Exit fullscreen mode

重要度:★★★★☆
发生可疑提交时。
pull 后，在很多提交的时候经常使用。

### 从其他分支带来文件/提交

```
git checkout {BRANCH_NAME} {FILE_NAME}

git cherry-pick {COMMIT_ID} 
```

Enter fullscreen mode Exit fullscreen mode

有用度:★★★☆☆
想偷另一个分支的工作人员制作的通用零件时。

### 修正之前的提交

```
git commit --amend # コミットメッセージも変えられる
git commit --amend --no-edit # vim をスキップ 
```

Enter fullscreen mode Exit fullscreen mode

有用度:★★★★☆
在“这个差分也想加入这个提交”的时候。

### 文件名的变更

```
git mv {OLD_NAME} {NEW_NNAME} 
```

Enter fullscreen mode Exit fullscreen mode

有用度:★★★★★
一般从资源管理器中名字的话会变成 delete 和 create。
如果这样更改文件名的话，日志会有变更，所以很高兴。
(这个也不是有选项，但是意外地使用的人很少... 因此入榜)

### 确认特定文件的历史

```
git log -- {FILE_NAME}
git blame {FILE_NAME} 
```

Enter fullscreen mode Exit fullscreen mode

有用度:★★★☆☆

虽然 blame 更有名，但是用 log 看也有方便的时候。

### 确认已合并的分支

```
git branch --merged
git branch --no-merged # その逆 
```

Enter fullscreen mode Exit fullscreen mode

有用度:★★★★☆
找到可以删除的本地分支。

### 指定了之前的分支，〇〇

```
git checkout - # ブランチ切り替え
git merge - #  マージ 
```

Enter fullscreen mode Exit fullscreen mode

有用度:★★★★★
说不定是最方便的。
分支名称太长，不能打的时候。
忘记分支名的时候。

### 合并后没有发生冲突

```
git merge --abort 
```

Enter fullscreen mode Exit fullscreen mode

有用度:★★★☆☆
在不询问别人就无法消除的时候。

### 看粗略的差分

```
git diff --stat 
```

Enter fullscreen mode Exit fullscreen mode

有用度:★★★☆☆
只显示文件名和该文件发生了多少行变化

### 观察转移区域的差分

```
git diff --cached 
```

Enter fullscreen mode Exit fullscreen mode

立场:★★★★☆
观察之前的提交和暂存区域的差分。
连接 add + commit 考虑的话是不需要的。 (这想法很危险，还是算了吧)
咦？ 请在意一下，提交了这个之后会变成什么远程的呢？

### 删除远程删除的本地分支

```
git remote prune origin 
```

Enter fullscreen mode Exit fullscreen mode

立场:★★★☆☆
branch 名称的预测转换有效的情况下，
会被随意转换为被删除的分支名称，
会不会有不愉快的感觉呢？ 我有，很多
有时候弄干净就和家一样舒服。

## 段子类

### 提交数量的排名

```
git shortlog --summary -n 
```

Enter fullscreen mode Exit fullscreen mode

乐趣:★★★★☆
努力想被别人认可的时候。
Git 在看你的提交数。

## 最后

怎么样？
你想在 git command 上更加享受吗？
如果能在 CLI 上进行 git 操作的话，也许能更开心地开发。
我很开心。

我觉得方便指令会很多。
shell 环境齐备后，我成了爱 git command 的人，想知道很多。
如果有“这个也要用哦”这样的保留指令的话，请告诉我。

## 再加上

### 暴露我的 git alias

```
＄ git config --list | grep alias
alias.b=branch
alias.s=status
alias.co=checkout 
```

Enter fullscreen mode Exit fullscreen mode

是做太多也没用的派。

非常感谢。