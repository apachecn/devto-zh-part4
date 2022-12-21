# git 初学者教程

> 原文：<https://dev.to/kavanozkafa/git-notes-3292>

# Git 教程

* * *

### **用户设置**

#### 了解用法手册

```
#git help 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置我们的用户名和邮箱

```
#git config --global user.name "sammas"
#git config --global user.email "sammas@gmail.com" 
```

Enter fullscreen mode Exit fullscreen mode

#### 显示所有配置

```
#git config --list 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### **创造**

#### 创建回购

```
#cd path_folder 
#git init 
```

Enter fullscreen mode Exit fullscreen mode

#### 克隆一个回购

```
#git clone repo_url 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### **局部变化**

#### 查看工作状态

```
#git status 
```

Enter fullscreen mode Exit fullscreen mode

#### 跟踪更改过的文件

```
#git diff 
```

Enter fullscreen mode Exit fullscreen mode

#### 添加文件到下一次提交

```
#git add -p <filename> 
```

Enter fullscreen mode Exit fullscreen mode

#### 提交所有文件

```
#git add .
this means take snapshots of all things and maybe you later fuck things up and takes it back. 
```

Enter fullscreen mode Exit fullscreen mode

#### 提交消息

```
#git commits -m "this is commit message" 
```

Enter fullscreen mode Exit fullscreen mode

#### 更改最后一次提交

```
#git commit --amend
dont do it to pushed commits 
```

Enter fullscreen mode Exit fullscreen mode

#### 搜索包含关键字的提交

```
#git log -S"config.menu_items" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### **地方历史**

#### 查看日志

```
#git log
#git log --author="sammas"
#git log -p file 
```

Enter fullscreen mode Exit fullscreen mode

#### 显示统计数据

```
#git log --stat 
```

Enter fullscreen mode Exit fullscreen mode

#### 打印出您的可视化日志

```
#git log --pretty=oneline --graph --decorate --all 
```

Enter fullscreen mode Exit fullscreen mode

#### 查看文件中谁修改了什么

```
#git blame file 
```

Enter fullscreen mode Exit fullscreen mode

#### 显示路径

```
#git log -p 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### **分支**

#### 列出分店

```
#git branch -av 
```

Enter fullscreen mode Exit fullscreen mode

#### 开关头分支

```
#git checkout <branch> 
```

Enter fullscreen mode Exit fullscreen mode

#### 根据你当前的头像创建一个新的分支

```
#git branch <newBranch> 
```

Enter fullscreen mode Exit fullscreen mode

#### 删除本地分支

```
#git branch -d <branch> 
```

Enter fullscreen mode Exit fullscreen mode

#### 标记提交

```
#git tag <tagName> 
```

Enter fullscreen mode Exit fullscreen mode

#### 从远程分支创建分支

```
#git checkout --track <remoteBranch> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### **发布**

#### 推送到 GitHub 仓库

```
#git remote add project_name https://github.com/user/repo.git 
#git push -u project_name branch(master vs) 
```

Enter fullscreen mode Exit fullscreen mode

#### 直接提交到存储库

```
#git commit -am "message" 
```

Enter fullscreen mode Exit fullscreen mode

#### 列出所有已配置的遥控器

```
#git remote -v 
```

Enter fullscreen mode Exit fullscreen mode

#### 显示关于遥控器的信息

```
#git remote show <remotename> 
```

Enter fullscreen mode Exit fullscreen mode

#### 从远程下载所有更改，但不集成到 HEAD 中

```
#git fetch <remote> 
```

Enter fullscreen mode Exit fullscreen mode

#### 从远程下载所有修改并合并到头

```
#git pull <remote> <branch> 
```

Enter fullscreen mode Exit fullscreen mode

#### 发布您的标签

```
#git push --tags 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### **合并**

#### 将分支合并到你当前的头上

```
#git merge <branch> 
```

Enter fullscreen mode Exit fullscreen mode

#### 将你当前的头部复位到分支上

```
#git rebase <branch> 
```

Enter fullscreen mode Exit fullscreen mode

#### abort rebase

```
#git  rebase --abort 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### **撤销**

#### 删除所有局部修改

```
#git reset --hard HEAD 
```

Enter fullscreen mode Exit fullscreen mode

#### 删除一个文件的修改

```
#git checkout HEAD <file> 
```

Enter fullscreen mode Exit fullscreen mode

```
#git reset --hard <commit>

preserve all changes as unstaged changes
#git reset <commit>

and preserve uncommitted local changes
#git reset --keep <commit> 
```

Enter fullscreen mode Exit fullscreen mode

#### 删除文件

```
#git rm file 
```

Enter fullscreen mode Exit fullscreen mode

#### 重命名文件

```
#git mv msg.txt mesaj.txt 
```

Enter fullscreen mode Exit fullscreen mode

#### 移动文件

```
#git mv msg.txt home\sammas\msg.txt 
```

Enter fullscreen mode Exit fullscreen mode

#### 从存储库中获取旧版本

```
#git log
#git checkout 0128dba..... -- index.html 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 创建别名

```
alias gitA="git add -A ."
alias gitC="git commit -m"
alias gitP="git push" 
```

Enter fullscreen mode Exit fullscreen mode

#### 列出所有 git 别名

```
#git config -l | grep alias | sed 's/^alias\.//g' 
```

Enter fullscreen mode Exit fullscreen mode

## [Git Notes](https://git-scm.com/docs/git-notes)

***添加或检查对象注释***

#### 列出关于对象的注释

```
git notes [list [<object>]] 
```

Enter fullscreen mode Exit fullscreen mode

#### 为给定对象添加注释(默认为头)

```
git notes add [-f] [--allow-empty] [-F <file> | -m <msg> | (-c | -C) <object>] [<object>] 
```

Enter fullscreen mode Exit fullscreen mode

#### 将第一个对象的注释复制到第二个对象上。

```
git notes copy [-f] ( --stdin | <from-object> <to-object> ) 
```

Enter fullscreen mode Exit fullscreen mode

#### 追加到已有对象的注释中(默认为 HEAD)。如果需要，创建新的 notes 对象。

```
git notes append [--allow-empty] [-F <file> | -m <msg> | (-c | -C) <object>] [<object>] 
```

Enter fullscreen mode Exit fullscreen mode

#### 编辑给定对象的注释(默认为 HEAD)。

```
git notes edit [--allow-empty] [<object>] 
```

Enter fullscreen mode Exit fullscreen mode

#### 显示给定对象的注释(默认为 HEAD)。

```
git notes show [<object>] 
```

Enter fullscreen mode Exit fullscreen mode

#### 将给定的音符参照合并到当前的音符参照中

```
git notes merge [-v | -q] [-s <strategy> ] <notes-ref> 
```

Enter fullscreen mode Exit fullscreen mode

#### 清除给定对象的注释(默认为头)

```
git notes remove [--ignore-missing] [--stdin] [<object>…​] 
```

Enter fullscreen mode Exit fullscreen mode

#### 删除不存在/不可达对象的所有注释。

```
git notes prune [-n] [-v] 
```

Enter fullscreen mode Exit fullscreen mode

#### 打印当前票据参考。

```
git notes get-ref 
```

Enter fullscreen mode Exit fullscreen mode