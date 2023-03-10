# 不要害怕埃谢尔

> 原文：<https://dev.to/gonsie/don-t-be-afraid-of-eshell-3p3d>

我决定改用 emacs 的原因之一是能够在我的编辑器中直接嵌入一个终端窗口。上周之前，我从未让嵌入式终端工作，也从未真正给过 eshell 太多的尝试。最初，我试图设置我的常规 shell ( [fish](https://fishshell.com) )，但是从来没有正确运行过。我还学到了 dired 的强大功能，并且不需要经常使用命令行。然后，上周，我发现自己在一个紧凑的循环中编辑和重新编译代码。我想在不离开 emacs 的情况下访问终端，触发编译。我创建了一个快速而肮脏的 eshell 设置…然后想为什么我没有早点这么做。

## 什么是 eshell？

Emacs shell 或 eshell 提供了直接构建在 Emacs 上的类似 bash 的命令行界面。它试图成为两个世界中最好的:

1.  一个 elisp REPL，其中可以直接执行 elisp 命令
2.  类似于`bash`的命令行界面

通过理解 eshell 以终端/shell 的方式呈现 emacs，我可以选择是以 elisp 还是 bash 的方式执行命令。更多详情可在 emacs 手册中的 [eshell 页面找到。](https://www.gnu.org/software/emacs/manual/html_mono/eshell.html)

## 裸露最小

首先:要启动一个 eshell 终端，运行`M-x eshell`。

接下来，要拥有一个基本的、正常运行的 shell 环境，我需要两件东西:

1.  有正确的路径
2.  有我最常用的别名

### 路径

eshell 使用的`PATH`变量通过`addpath`命令公开。没有任何参数，`addpath`简单地打印出当前在`PATH`中的目录。当使用一个或多个参数调用时，`addpath`会将这些路径添加到您的`PATH`中。不需要包含当前的`PATH`内容，因为`addpath`总是追加内容。

### 别名

Eshell 有许多内置命令，类似于 [bash 内置的](https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Shell-Builtin-Commands)。最有用的命令是`alias`，它可以用来创建您习惯在常规 shell 中使用的任何命令。对我来说，第一步是找出我最常用的别名，并将它们添加到 eshell 中。

首先，我需要我正在使用的另一种选择。幸运的是，有一个由 eshell 提供的`ls`(通过运行`which ls`显示)。它采用了和我的 Mac 内置版本相同的参数。

接下来，我需要一种方法来打开包含特定文件的 emacs 缓冲区。我使用命令`em`在我的终端窗口(`emacs -nw`)中启动一个新的 emacs 会话。eshell 的别名略有不同，因为我已经*在*运行 emacs 会话。我只想开始编辑一个特定的文件。因此，别名调用了 emacs 函数`find-file`。

我的别名文件(`~/.emacs.d/eshell/alias`)看起来是这样的:

```
alias la ls -AFGhl $1
alias em find-file $1 
```

## 下一步

最低限度照顾我最频繁的用例。现在，我可以不用离开 emacs 就能编译代码了！

我的下一步是将别名添加到[点文件](https://github.com/gonsie/dotfiles)中，并继续改进我的 eshell 设置。快速的网络搜索让我在 [howardabrams/dot-files](https://github.com/howardabrams/dot-files/blob/master/emacs-eshell.org) 中找到了一个关于 eshell 的文字配置。这里有几个我必须自己添加的设置。