# 我如何在 Bash 上设置 Git 别名

> 原文：<https://dev.to/jessbudd/how-i-set-up-git-aliases-on-bash-5ejn>

大约 2 年前，我第一次在教程中看到有人使用别名作为他们的 git 命令。我当时认为这是我应该设置的东西，但由于对命令行不太熟悉，我没有深入研究。

## 别名是什么？

对于那些以前可能没有听说过别名的人来说，它们就像是终端中的快捷命令。您可以键入快捷方式，而不是键入整个命令。

例如，每当我想要推送我的 repo 时，我可以为该命令分配一个短代码，并简单地键入`gp`(或我设置的任何快捷键)来做同样的事情，而不是键入`git push`。

神奇！

## 设置别名

本周，我想是时候设置这些化名了，于是我去了谷歌。我找到了一堆关于如何创建 git 别名的帖子，比如[这个](https://stackoverflow.com/questions/2553786/how-do-i-alias-commands-in-git)和[这个](https://githowto.com/aliases)，但是被不同的选项弄得有点不知所措，而且和大多数教程一样，它们假设了相当多的预先知识。

按照最简单的格式示例，我将最常见的 git 操作输入到我已经为当前项目打开的命令行窗口中。

```
alias gs='git status '

alias ga='git add '

alias gc='git commit -m '

alias gp='git push'

alias gb='git branch '

alias go='git checkout '

alias gm='git merge '

alias gpl='git pull'

alias gl='git log' 
```

然后我用一个`gs`试探性地测试了我闪亮的新化名。

成功了！万岁。

我沿着我的快乐之路走。

直到第二天，当我再次启动我的开发环境并尝试使用我的新别名时。它们不起作用。

```
-bash: gs: command not found 
```

*哭丧着脸*。

## 全球设置别名

 *原来我只创建了临时别名，这些别名只存在于那个 sinlge CLI 窗口中。为了创建将在所有未来窗口中持续的全局别名，我需要将它们添加到我的`.bash_profile`中。

在 mac 上，`.bash_profile`文件位于 home username 文件夹中。这是一个隐藏文件，所以我需要通过按下`cmd + shft + .`找到它来显示所有隐藏文件。

所以在我的`.bash_profile`文件中，我输入了和前一天完全一样的代码。

```
alias gs='git status '
alias ga='git add '
alias gc='git commit -m '
alias gp='git push'
alias gb='git branch '
alias go='git checkout '
alias gm='git merge '
alias gpl='git pull'
alias gl='git log' 
```

我保存了文件，关闭了所有的终端窗口，并在一个新的窗口中测试了别名，它们工作了。耶！

## 其他方法

在接下来的几天里(感谢 twitterverse ),我发现可能有更好的方法来做到这一点。例如，Atlassian 有一篇很好的文章，描述了如何通过 gitconfig 文件设置别名。

提到的一个挡泥板 [oh-my-zsh 带有内置的 git 别名](https://ohmyz.sh/)。另一个人提到将 git 别名放在一个单独的别名文件中，然后在。bashrc 文件。

我肯定有很好的理由选择一种方法而不是另一种，但现在我对我的方法很满意，因为它适合我的情况。*