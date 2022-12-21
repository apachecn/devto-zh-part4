# 使用 vim 避免手动复制信息

> 原文：<https://dev.to/voyeg3r/avoiding-manual-copy-of-infromation-with-vim-1k91>

# 简介

很多时候，我们需要从系统剪贴板复制信息。对于这些情况，我们可以使用一个简单的 grep plus (pbcopy | xclip ),但是如果您使用的是已经打开的文件，或者如果您所在的系统没有 GNU utils，那么您可以简单地使用 vim 来完成这个任务

## 问题

假设您必须从 vim 上所有打开的文件中复制具有特定模式的所有代码行:

```
vim * text.txt 
```

Enter fullscreen mode Exit fullscreen mode

使用 **:args** 命令，我们可以看到所有打开文件的列表。

```
:args 
```

Enter fullscreen mode Exit fullscreen mode

## 解

我们将开始清理去皮重的寄存器，这是一种许多 vim“剪贴板”,接受附加一个特殊的技巧。

```
:let @a="" 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的命令，我们确保寄存器`a`为空

```
:argdo g/pattern/yank A 
```

Enter fullscreen mode Exit fullscreen mode

`argdo`命令对我们所有打开的文件执行任务，神奇的事情发生在全局命令上。OBS:使用`A`允许我们将包含**模式**的所有文件的每一行添加到寄存器`a`中。

现在让我们结束:

```
:let @+=@a 
```

Enter fullscreen mode Exit fullscreen mode

OBS:如果你只是试图使用`:argdo g/pattern/yank +`将不会工作，因为系统剪贴板不允许“追加”任何内容给它。

## 用`grep`会是什么样子？

```
grep 'pattern' *.txt | xclip -selection clipboard 
```

Enter fullscreen mode Exit fullscreen mode

## 创建别名以便于操作

在我的情况下，我使用 zsh 和我的别名文件，我有

```
(( $+commands[xclip] )) && {
    alias pbpaste='xclip -i -selection clipboard -o'
    alias pbcopy='xclip -selection clipboard'
} 
```

Enter fullscreen mode Exit fullscreen mode