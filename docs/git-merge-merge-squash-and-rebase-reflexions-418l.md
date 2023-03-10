# Git 合并，合并-挤压和重置简化

> 原文：<https://dev.to/hectorpascual/git-merge-merge-squash-and-rebase-reflexions-418l>

# Git merge，merge-squash 和 rebase 简化

## 用基本档案启动仓库

让我们创建一个空存储库并将文件提交到主分支:

```
$ git init 
$ touch m1
$ git add m1
$ git commit -m "m1" 
```

现在我们切换到一个名为 feature 的新分支:

```
$ git checkout -b feature 
```

是时候在特性分支中创建一个文件并提交它了:

```
$ touch f1 
$ git add f1 
$ git commit -m "f1" 
```

现在让我们回到主文件并创建另一个文件:

```
$ git checkout master
$ touch m2 
$ git add m2
$ git commit -m "m2" 
```

如果我们键入`$ git log`,我们会看到:

```
* 60c3a2b - (1 second ago) m2 - Hector (HEAD -> master)
| * 7832858 - (87 seconds ago) f1 - Hector (feature)
|/  
* 0a950f5 - (5 minutes ago) m1 - Hector 
```

## 集成从主分支到特征分支的变化

到目前为止，我们有一个简单的功能分支，在主分支中有一个提交和两个提交，通常的做法是将主分支中的提交集成到我们的功能中，以避免丢失对主更改的跟踪，但有几种方法可以做到这一点:

*   **Git merge**

```
$ git checkout feature 
$ git merge master 
```

这是一种非破坏性的方式，分支保留其历史记录，并且只出现一个合并提交:

```
*   fad9250 - (11 seconds ago) Merge branch 'master' into feature - Hector (HEAD -> feature)
|\  
| * 60c3a2b - (5 minutes ago) m2 - Hector (master)
* | 7832858 - (7 minutes ago) f1 - Hector
|/  
* 0a950f5 - (11 minutes ago) m1 - Hector 
```

*   **Git 合并-挤压**

```
$ git checkout feature 
$ git merge --squash master
$ git commit -m "Squash" 
```

这也是一种非破坏性的方式，分支保留其历史记录，但现在不会出现合并提交，master 中的所有更改都被分组到一个名为 Squash 的提交中，并在 feature 分支中显示为单个提交，如您所见:

```
* dc4e5b7 - (10 seconds ago) Squash - Hector (HEAD -> feature)
* 7832858 - (19 minutes ago) f1 - Hector
| * 60c3a2b - (18 minutes ago) m2 - Hector (master)
|/  
* 0a950f5 - (23 minutes ago) m1 - Hector 
```

*   **Git rebase**

```
$ git checkout feature 
$ git rebase master 
```

相反，使用 rebase，历史将更简单，它在高级语言中所做的是搜索最后一个公共提交，并在其上添加主提交，但在我们的分支提交之下。

如您所见，m2 提交是在 m1 之上的**(两个分支中的最后一次公共提交)和在** f1 之下的**(我们的特色分支提交)**

```
* 6074794 - (11 minutes ago) f1 - Hector (HEAD -> feature)
* 60c3a2b - (10 minutes ago) m2 - Hector (master)
* 0a950f5 - (15 minutes ago) m1 - Hector 
```

## 整合从特征分支到主特征的变化

假设我们通过合并将主更改集成到特征分支，并且我们将一些更改添加到两个分支，m3 和 f2 产生了这个日志:

```
* 83c98e1 - (1 second ago) m3 - Hector (HEAD -> master)
| * 0a3663c - (2 minutes ago) f2 - Hector (feature)
| *   edfda02 - (2 minutes ago) Merge branch 'master' into feature - Hector
| |\  
| |/  
|/|   
* | 60c3a2b - (29 minutes ago) m2 - Hector
| * 490a167 - (3 minutes ago) f1 - Hector
|/  
* 0a950f5 - (35 minutes ago) m1 - Hector 
```

和以前一样，我们有 3 个相同的选项:

*   **Git merge**

如果我们做一个`$ git merge feature`，结果如下:

```
*   d6b643b - (4 seconds ago) Merge branch 'feature' - Hector (HEAD -> master)
|\  
| * 0a3663c - (4 minutes ago) f2 - Hector (feature)
| *   edfda02 - (4 minutes ago) Merge branch 'master' into feature - Hector
| |\  
| * | 490a167 - (4 minutes ago) f1 - Hector
* | | 83c98e1 - (2 minutes ago) m3 - Hector
| |/  
|/|   
* | 60c3a2b - (31 minutes ago) m2 - Hector
|/  
* 0a950f5 - (36 minutes ago) m1 - Hector 
```

*   **Git 合并-挤压**

如果我们做一个壁球:

```
$ git merge --squash feature
$ git commit -m "Squash" 
```

通过预期的行为，而不是合并提交和非破坏性历史，我们得到:

```
* 63f7387 - (10 seconds ago) Squash - Hector (HEAD -> master)
* 83c98e1 - (3 minutes ago) m3 - Hector
| * 0a3663c - (5 minutes ago) f2 - Hector (feature)
| *   edfda02 - (5 minutes ago) Merge branch 'master' into feature - Hector
| |\  
| |/  
|/|   
* | 60c3a2b - (32 minutes ago) m2 - Hector
| * 490a167 - (5 minutes ago) f1 - Hector
|/  
* 0a950f5 - (37 minutes ago) m1 - Hector 
```

*   **Git rebase**

最后，让我们测试一下使用`$ git rebase feature`会发生什么，我们获得了下面的日志:

```
* 6c6ba0e - (5 minutes ago) m3 - Hector (HEAD -> master)
* 0a3663c - (7 minutes ago) f2 - Hector (feature)
*   edfda02 - (7 minutes ago) Merge branch 'master' into feature - Hector
|\  
| * 60c3a2b - (35 minutes ago) m2 - Hector
* | 490a167 - (8 minutes ago) f1 - Hector
|/  
* 0a950f5 - (40 minutes ago) m1 - Hector 
```

Rebase 获取最后一个公共提交，即合并提交，add 将 f2 提交置于其上，但在来自 master 的 m3 提交之下。