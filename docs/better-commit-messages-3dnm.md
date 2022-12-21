# 最好提交消息

> 原文：<https://dev.to/thefern/better-commit-messages-3dnm>

## 更好地提交消息

浏览我的 TL 时看到了下面的 tweet，我觉得有点被攻击了。在我的大多数项目中，我都是一个人完成的，我总是做典型的一行程序，init、update、readme、fix-01 等等。在本文中，我将向您展示如何配置 git 在 vscode 上启动您的模板，以获得更好的提交消息。

> ![Ben Orenstein profile image](img/9295833c72b6a0e8fb5b33aed7fb02fd.png)本·奥伦斯坦@ r00k![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)大多数提交消息几乎毫无用处，因为它们关注的是做了什么而不是为什么。
> 
> 这恰恰是错误的关注点。你总是可以重构一个提交包含了什么样的改变，但是几乎不可能挖掘出它被完成的原因。
> 
> (线程)2019 年 9 月 17:33 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1175100703829909505)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1175100703829909505)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1175100703829909505)

## 模板

这是我目前使用的模板，你可以根据自己的喜好随意修改。这里有一个我使用它的例子。在开始的时候，它确实会让你思考该写什么，但是在几次尝试之后，你会找到窍门的。

```
# Commit Title (50 chars)

# Why? Issue, enhancement, etc.

# How does it address the why?

# Tickets, Links, etc... 
```

Enter fullscreen mode Exit fullscreen mode

## 将模板保存在您的主目录中

我把我的模板命名为`.git_commit_msg.txt`，但是你可以随意命名。如果没有环境主路径，那么使用一个绝对路径。

### Linux，Mac

`${HOME}/.git_commit_msg.txt`

### 窗口

`%HOME%/.git_commit_msg.txt`

## 在 git 上激活模板

### Linux，Mac

```
git config --global commit.template ${HOME}/.git_commit_msg.txt 
```

Enter fullscreen mode Exit fullscreen mode

### 窗口

```
git config --global commit.template %HOME%/.git_commit_msg.txt 
```

Enter fullscreen mode Exit fullscreen mode

## 配置你的编辑器

[core.editor 文档](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coreeditor)

检查您的默认文本编辑器:

```
git config core.editor 
```

Enter fullscreen mode Exit fullscreen mode

我在 git 配置上用 vscode 配置了我的:

```
git config --global core.editor "code -w" 
```

Enter fullscreen mode Exit fullscreen mode

> 代码需要在 path 上，如果你在终端/提示符下键入`code`而编辑器没有打开，那么打开 vscode 做 CMD/CTRL+SHIFT+P，键入`shell command`，你应该会看到安装和卸载选项。默认情况下，Windows 会在安装过程中添加代码。
> 
> 代码可以替换为您喜欢的文本编辑器，vim，emacs，sublime，如果您的文本编辑器有问题，[因此](https://stackoverflow.com/search?q=core.editor+git)是您的朋友。

## 它是如何工作的？

提交后，模板将在 vscode 上打开，您可以取消模板行的注释，添加新行，等等。保存消息，并关闭文件。此时，您的提交将完成。如果您关闭模板而不添加任何内容，提交将被取消。当您进行 PR 时，整个详细的提交消息将会在您的 PR 中，准备好供维护人员查看。

```
git commit 
```

Enter fullscreen mode Exit fullscreen mode

> 对于那些讨厌的一行程序，您仍然可以使用`-m`参数。

## 你更喜欢什么？

*   你喜欢一个班轮，然后做详细的公关。
*   你使用提交模板吗？
*   你的团队/公司是否强制执行一个模板？
*   还有其他方法吗？

我希望你喜欢这篇文章，感谢阅读！