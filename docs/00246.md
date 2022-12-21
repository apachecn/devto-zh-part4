# n350071 Git 票据

> 原文：<https://dev.to/n350071/my-git-note-5259>

## 👍历史

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 历史阅读

### n350071🇯🇵10 月 17 日 193 分钟阅读

#git](/n350071/git-history-reading-42k5)

## 👍合并

### 合并

```
git merge # not make a merge commit
git merge --no-ff # make a merge commit (to be able to revert it later)

# check conflict before merge
git merge --no-commit [branch name]

# abort
git merge --abort 
```

Enter fullscreen mode Exit fullscreen mode

### 冲突

```
# マージの複雑なコンフリクトのときに使う
git checkout --conflict=diff3 file.rb # Baseコンフリクトマーカーも表示する
git checkout --conflict=merge file.rb # コンフリクトマーカーの書き直し

# cancel our modified
git checkout develop -- file/to/path.rb

# cancel their modified
git checkout head -- file/to/path.rb 
```

Enter fullscreen mode Exit fullscreen mode

### 回复原状

```
# revert the merge commit and back to the first parent (the modified from x in 'git merge x' is deleted)
git revert -m 1 7143dc9d8d835efa3012e9ad624c75965297ee88 -n
git revert --continue

# completely delete the merge commit (You shouldn't do it after you pushed the merge commit)
git reset --hard HEAD^

# revert the reset --hard HEAD^
git reflog -n 3 # list your operations
git reset --hard 9823val1 # back to the operation 
```

Enter fullscreen mode Exit fullscreen mode

## 👍拉取请求

### 发出空拉请求

```
git commit --allow-empty -m [the title of the Pull Request]
git push origin [branch name]
git branch -u origin/[remote branch name]
git branch -vv # confirm 
```

Enter fullscreen mode Exit fullscreen mode

## 👍分支管理

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## [Git]删除[Local | Remote]合并分支

### n350071🇯🇵9 月 19 日 1911 分钟阅读

#git](/n350071/git-show-and-delete-merged-branch-even-if-it-s-squashed-58il)

### 重命名

#### 本地

```
# rename the branch name (git branch (-m | -M) [<oldbranch>] <newbranch>)
git branch -m [new name]
git branch -m [old name] [new name] # This is also ok. 
```

Enter fullscreen mode Exit fullscreen mode

#### 遥控

```
# remove old branch in remote
git push origin :[old name]

# push the renamed branch
git push origin [new name] 
```

Enter fullscreen mode Exit fullscreen mode

### 搜索远程分支

```
git branch -a | grep [search-word] 
```

Enter fullscreen mode Exit fullscreen mode

### 从远程分支结账

```
git fetch
git checkout -b branch_name origin/other_name 
```

Enter fullscreen mode Exit fullscreen mode

## 👍开发~提交

### 藏匿

```
# save include untracked files
git stash --include-untracked

# save with name
git stash save 'stash_name'

# show diff
git stash show -p stash@{0}

# apply
git stash apply stash@{0}

# apply & remove
git stash pop stash@{0} 
```

Enter fullscreen mode Exit fullscreen mode

### 干净

```
git  clean -dfn # check
git  clean -df  # remove un tracked files

git rm --cached file/to/path.rb # remove a tracked file 
```

Enter fullscreen mode Exit fullscreen mode

### 差异

```
# Show diff for the staged files
git diff --cached

# Show diff in an editor
git diff HEAD^ --name-only | xargs atom 
```

Enter fullscreen mode Exit fullscreen mode

### 提交

```
# Rewrite the previous commimt message
git commit --amend

# Add the staged files to the previous commit
git commit --amend --no-edit

# squash
git rebase -i [previous_commit_id] 
```

Enter fullscreen mode Exit fullscreen mode

## salvage (复活，撤销)

### 删除文件

找到删除已删除文件的提交，并从中签出。

```
git rev-list -n 1 HEAD -- deleted/file.rb
git checkout commit_id^ -- /deleted/file.rb 
```

Enter fullscreen mode Exit fullscreen mode

### 从 git 复位中恢复、复兴——硬 HEAD^

找到日志，并重置【重置】的

```
git reflog -n 3
git reset --hard "HEAD@{1}" 
```

Enter fullscreen mode Exit fullscreen mode

## 👍知识库管理

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## [git]保留空目录。gitkeep

### n350071🇯🇵10 月 13 日 191 分钟阅读

#git](/n350071/git-keep-empty-directory-by-gitkeep-aj1)

### 克隆并重命名目录

```
# git clone url [directory-name]
git clone git@github.com:n350071/rails-on-k8s.git rails-on-k8s-refactor 
```

Enter fullscreen mode Exit fullscreen mode

## 👍环境

### GitHub

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 生成 GitHub ssh 密钥并上传。

### n350071🇯🇵9 月 25 日 191 分钟阅读

#git](/n350071/generate-github-ssh-key-and-upload-it-586f)

### 远程存储库

```
# check the remote repository
git remote -v

# add (git remote add [name] [url])
git remote add origin git@github.com:n350071/rails-starter-kit-with-docker.git

# Change remote name from 'origin' to 'destination'
$ git remote rename origin destination

# change the default remote repository for 'push'
git push -u <remote_name> <local_branch_name> 
```

Enter fullscreen mode Exit fullscreen mode

### .gitconfig

```
$ cat .gitconfig
[alias]
  check = !git checkout $(git branch | sed 's/*//g' | sed 's/ //g' | peco)
  bcp  = !git branch | peco | sed 's/*//g' | sed 's/ //g' | tr -d '\n'  | pbcopy
[log]
  date = iso-local
[core]
  quotepath = false
[commit]
  template = /Users/naoki/.git_commit_template 
```

Enter fullscreen mode Exit fullscreen mode

### [T1。git 提交模板](#gitcommittemplate)

```
$ cat .git_commit_template

# === format ===
# :emoji: issue-id issue-title

# ==== example ====
# :+1: SR-400 認証機能の実装
# :bug: BG-128 Thanksメッセージは２通ではなく１通

# ==== Emojis ====
# 👍 小さな機能追加 :+1:
# 🎉 大きな機能追加 :tada:
# 🐛 バグ修正 :bug:
# 💚 リファクタリング  :green_heart:
# ✅ テストの追加 :white_check_mark:
# 📝 ドキュメント追加 :memo:
# 🚀 パフォーマンス改善 :rocket:
# 🆙 バージョンアップ :up:
# 😅 動くようにした :sweat_smile:
# ⛔️ 下書き :no_entry:
# ✏️ 記事投稿 :pencil: 
```

Enter fullscreen mode Exit fullscreen mode