# 构建一个 Shell 函数来复制最新的 Git Commit SHA

> 原文：<https://dev.to/alexmacarthur/building-a-shell-function-to-copy-the-latest-git-commit-sha-35nf>

我经常发现自己需要复制项目中最新提交的 SHA，并且手动地做这件事变得很麻烦。我已经对通过滚动自定义 shell 函数来提高效率感到越来越舒服和兴奋了([就像我在这里写的](https://dev.to/posts/formatting-my-php-more-efficiently-with-a-bash-function))，所以这感觉像是另一个很好的候选。经过一番搜索，这一点得到了证实——很少有其他人构建一个简单的终端命令来做这样的事情。所以，我去了。

**问题:**复制我上一次提交的 SHA 很麻烦，而且很费时间。

**解决方案:**创建一个 shell 函数，允许我用一个简单的终端命令来完成这个任务。

## 建筑功能

我为此做的大部分实验都是在我的机器上的常规 shell 脚本中完成的。但是为了快速测试作为终端命令的脚本，我将我的迭代复制到我的`~/.zshrc`中，并用`source ~/.zshrc`来获取它。随着时间的推移，这些迭代就是这样进行的。

### 迭代#1::愚蠢简单

设置这个函数的基础非常简单:

```
# `clc` stands for `copy last commit`.
function clc {
    LAST_COMMIT_SHA=$(git rev-parse HEAD)
    echo "$LAST_COMMIT_SHA" | tr -d '\n' | pbcopy
    echo "Copied ${LAST_COMMIT_SHA}."
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，我可以在我的终端中运行`clc`，通过这样做，获取最后一个提交 SHA，删除换行符，将其复制到剪贴板，并显示一条漂亮的确认消息。完成任务！酪

### 迭代#2::从不同的分支复制

当我挑选时，我经常想要不同分支的最新提交 SHA，而不是我当前的。所以，我升级了函数，接受一个分支名称作为参数。如果设置了该参数，则签出该分支，获取最新的提交 SHA，并返回到原始分支。

```
function clc { +    # The original script wrapped in a nested function:
+    function copy_last_commit() {
        LAST_COMMIT_SHA=$(git rev-parse HEAD)
        echo "$LAST_COMMIT_SHA" | tr -d '\n' | pbcopy
        echo "Copied ${LAST_COMMIT_SHA}."
+    }
+    
+    # Added to check out branch, if parameter is set.
+    if [ ! -z "$1" ]; then
+        if git checkout $1 >/dev/null; then
+            copy_last_commit
+            git checkout - >/dev/null
+        else
+            echo "Checkout wasn't successful. Didn't copy anything."
+        fi
+    else
+        copy_last_commit
+    fi } 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以运行类似于`clc some-branch`的东西。这将导致该分支被检出(`git checkout $1 >/dev/null`)，如果成功，复制将开始，产生如下内容:

```
Switched to branch 'my-branch'
Copied 3ccbd742f916659c50cbff6c2f63e2ba28a168b5.
Switched to branch 'master' 
```

Enter fullscreen mode Exit fullscreen mode

如果我偶然传递了一个不存在的分支名，而它失败了，就会输出一条错误消息。当然，如果没有传递分支参数，just 检查会直接复制当前分支的提交 SHA。但是！

### 迭代#3::尊重未分级的变更

通常，我的活动分支有一堆未分级的变更，运行这个命令从一个不同的分支获取阿沙会产生这个消息:

```
Please commit your changes or stash them before you switch branches.
Aborting
Checkout wasn't successful. Didn't copy anything. 
```

Enter fullscreen mode Exit fullscreen mode

所以，我再次升级它——这一次，不是完全检出分支，而是使用`git stash`来隐藏我们的更改并恢复它们。它更加灵活，不需要你有一个干净的工作目录。

在这样做的时候，重要的是我们只在真正需要的时候`stash`。当没有东西可以存储时运行`git stash`，*什么都不做*——没有新的存储被创建。如果我们在一个新的仓库没有实际创建的时候自动运行`git stash pop`，我们可能最终会恢复一个我们不想要的旧仓库(你可以使用`git stash list`查看你的回购中的所有仓库)。

我使用`git status -s`执行了这个检查，并将其保存到变量`IS_DIRTY`中。如果工作树是“脏的”，隐藏更改，并在我们完成后恢复它们。

```
function clc {
    # The original script wrapped in a nested function:
    function copy_last_commit() {
        LAST_COMMIT_SHA=$(git rev-parse HEAD)
        echo "$LAST_COMMIT_SHA" | tr -d '\n' | pbcopy
        echo "Copied ${LAST_COMMIT_SHA}."
    }

    # Added to check out branch, if parameter is set.
    if [ ! -z "$1" ]; then
+        IS_DIRTY=$(git status -s)
+    
+        if [[ ! -z $IS_DIRTY ]]; then
+            git stash push -u >/dev/null
+            echo "Stashed unstaged stages."
+        fi 
        if git checkout $1 >/dev/null; then
            copy_last_commit
            git checkout - >/dev/null
        else
            echo "Checkout wasn't successful. Didn't copy anything."
        fi

+        if [[ ! -z $IS_DIRTY ]]; then
+            git stash pop >/dev/null
+            echo "Restored unstaged changes."
+        fi
    else
        copy_last_commit
    fi
} 
```

Enter fullscreen mode Exit fullscreen mode

因为它让我犯了错误，所以要特别注意，我没有使用简单的`git stash`命令来隐藏我的更改。相反，我使用的是`git stash push -u`。这是因为我想保存我当前所有的修改，包括我刚刚创建但还没有提交的文件。更冗长的`git stash push`后面跟着`-u`(代表`--include-untracked`)标志就能做到这一点。

## 迭代#4::拆掉&基本上一切

在这一点上，我对自己感觉很好。我已经解决了一路上遇到的所有奇怪的 shell 问题，并在整个过程中发表了这篇博文。

然后，几个 Reddit 用户(谢谢，[努勒](https://www.reddit.com/user/nunull/)和[奥斯汀-谢弗](https://www.reddit.com/user/austin-schaffer/)！)指出，我实际上并不需要*来执行检验，只是为了获得提交 SHA。这应该是显而易见的，因为已经在使用`git rev-parse HEAD`来拉 SHA。用我需要的任何分支替换掉`HEAD`就已经完成了，完全不需要任何复杂的结帐和存储逻辑🤦。*

有了这个启示，函数从所有的代码行减少到只有几行:

```
function clc {
    [[ -z $1 ]] && BRANCH=$(git rev-parse --abbrev-ref HEAD) || BRANCH=$1
    LAST_COMMIT_SHA=$(git rev-parse $BRANCH | tail -n 1)
    echo "$LAST_COMMIT_SHA" | tr -d '\n' | pbcopy
    echo "Copied ${LAST_COMMIT_SHA}."
} 
```

Enter fullscreen mode Exit fullscreen mode

看，Reddit 没那么糟！再次感谢喊出这句话的两个人！

## 见修饰过的最终产品

我在这个版本中添加了一些漂亮的终端颜色。

## 使用功能

要在您的本地 shell 中使用它，您可以将它放在您的`~/.bashrc`或`~/.zshrc`文件中，但是最好将它存储在系统中的其他地方。我不能代表`bash`的用户，但是如果你使用的是`zsh`，那么只需要把文件放在你的`$ZSH/custom`目录中，然后找到它。

### 安装为自定义 ZSH 功能

为了使它变得非常简单，运行下面的命令，它将从我的 GitHub Gist 中检索这个函数，并把它放到适当的位置:

```
curl https://gist.githubusercontent.com/alexmacarthur/933a50c3e072baaf7b6ed18b94e0e873/raw/59f22ae740d83f39a88b70f4aebb0c27b2f9805f/copy-last-commit.zsh -o $ZSH/custom/clc.zsh 
```

Enter fullscreen mode Exit fullscreen mode

### 运行命令

之后，用`source ~/.zshrc`启动它，您应该能够运行这个命令了。

运行`clc`将会返回如下内容:

```
Copied 3ccbd742f916659c50cbff6c2f63e2ba28a168b5 from master. 
```

Enter fullscreen mode Exit fullscreen mode

运行`clc new-branch`将会返回如下内容:

```
Copied 3ccbd742f916659c50cbff6c2f63e2ba28a168b5 from new-branch. 
```

Enter fullscreen mode Exit fullscreen mode

## 我错过了什么吗？

我不会感到惊讶。如果有什么建议或改进，请告诉我！