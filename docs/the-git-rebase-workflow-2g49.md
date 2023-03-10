# Git Rebase 工作流

> 原文：<https://dev.to/shostarsson/the-git-rebase-workflow-2g49>

大家好，

在互联网上寻找完全不同的东西，我看到了一个关于“Git Rebase”工作流的视频，它不同于“标准”的“Git 合并/拉取请求”工作流。视频中的开发者很好地解释了它为什么有趣，以及它在实践中是如何工作的。

[https://www.youtube.com/embed/f1wnYdLEpgI](https://www.youtube.com/embed/f1wnYdLEpgI)

这看起来非常有趣，因为当使用拉请求/合并工作流时，Git 树确实会变得非常大。

我想听听你对 Git 工作流程的反馈。

你在你的项目中用过它吗？无论你是独自完成你的项目还是与其他开发人员合作。

我很高兴得到您对 Git 工作流的反馈。

我很快在一个测试回购上使用它，看看它是如何工作的，它很有趣。

* * *

### 试试看

如果您想测试它，您可以使用下面的脚本准备好一个本地 git 文件夹，这样您就可以试验 git rebase 工作流:

```
#!/bin/zsh

git branch "feature/1"
git checkout "feature/1"

echo "Feature 1 : " >> README.md
echo "1" >> README.md
git commit -a -m "Feature 1 : 1"

echo "2" >> README.md
git commit -a -m "Feature 1 : 2"

echo "3" >> README.md
git commit -a -m "Feature 1 : 3"

git checkout "master"

echo "Master" >> README.md
echo "1" >> README.md

git commit -a -m "Master 1 : 1" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### Git Rebase 工作流程

然后，要使用 Git Rebase 工作流，您可以尝试:

```
# Checkout the Feature branch.
git checkout feature/1

# Rebase the Feature branch onto "master" the interactive way.
git rebase -i master

#Resolve conflicts

# Continue rebasing now that the conflicts are resolved.
# Squash your commits into the first one and reword the first commit to explain all the changes from the 3 commits modifications.
git rebase --continue

# Checkout the "master" branch.
git checkout master

# Rebase the master branch onto "master".
git rebase feature/1

# Delete the Feature branch.
git branch -D feature/1 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### Git 合并(挤压提交)工作流

如果你想使用 git merge 得到同样的结果，你可以试试:

```
# Move to the master branch.
git checkout master

# Merge the "feature/1" branch and squash the commits.
git merge --squash feature/1

#Resolve conflicts

# Commit your changes and add a single commit message for all your commits.
# You can omit the "-m" to have a template popping up based on your previous commit messages.
git commit -m "Feature 1 : 1, 2 et 3"

# Delete the "feature/1" branch that is no longer needed.
git branch -D feature/1 
```

Enter fullscreen mode Exit fullscreen mode

干杯。

* * *

视频由 [Wild & Secure](https://wildnsecure.com) 制作，这是您的安全和房地产咨询公司。
如果你想每周收到高质量的安全内容，请在我们的网站上订阅我们的时事通讯[。](https://wildnsecure.com/contact)