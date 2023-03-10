# 爱你的终端指南

> 原文：<https://dev.to/lazar/a-guide-to-loving-your-terminal-2hdj>

*祝贺您，您是一名自豪的终端所有者！*

刚转 Linux 的时候，我像躲避瘟疫一样躲避我的终端。现在我都不知道没有终端我是怎么过的！

但我认为，我们可以做更多的事情，让我们的体验更轻松。

### 编写 Shell 脚本

假设您有一长串需要运行的命令，但现在有足够的时间坐在那里等着一次输入一个命令。一个简单的 shell 脚本就可以解决这个问题！但是 shell 脚本不仅仅是命令。

因为我用 Bash。我会专注于巴什。一些人没有意识到 Bash 是一种全功能的脚本语言。它有 while 循环、if 语句、接受输入，并且基本上做任何其他脚本语言能做的事情。这对于在你的电脑上实现自动化非常有用。让 shell 脚本在启动时开始运行也非常容易，如果您不想让它在后台运行，这是非常完美的。

我有一个笑话，我已经自己构建了 AI，因为我一次运行了大量的 shell 脚本。

### 使用“& &”运算符

&&运算符将两个终端命令变成一个。例如:

```
echo "hello" && echo "Lazar" 
```

Enter fullscreen mode Exit fullscreen mode

这只是在终端中打印出来的:

```
"hello"
"Lazar" 
```

Enter fullscreen mode Exit fullscreen mode

给聪明人一句话，这不是串联！它分别运行这两个命令，这就是为什么它打印在两个不同的行。

你能用它做什么呢，它真的为我节省了很多时间。当我使用 Debian 的时候，当我安装一些东西的时候，有时候需要很长时间来更新软件库，然后我不得不停下来输入命令来安装一些东西。所以我只要输入:

```
sudo apt-get update && sudo apt install 
```

Enter fullscreen mode Exit fullscreen mode

### 改变终端字体

***下面评论下来，如果你在你的终端上改变字体。*** 因为我不知道有谁知道。现在，我不能评论外面的每一个终端，但对于 gnome，deepin 和 tilix 来说，这真的很容易。**首选项= >通用= >字体**。我发现如果你想让终端不那么吓人，就用一种对你来说更平静的字体。此外，在 deepin 中，您可以更改终端的主题和不透明度。

所以现在就这些了。我想我可能不得不在这个星期写一些这样的文章，因为我正在为两个不同的人做两个不同的项目，现在真的不能做任何壮观的事情。

祝你们今天愉快！