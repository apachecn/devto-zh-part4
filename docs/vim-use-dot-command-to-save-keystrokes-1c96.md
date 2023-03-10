# 使用 Vim 的 dot 命令更快地完成重复性任务

> 原文：<https://dev.to/iggredible/vim-use-dot-command-to-save-keystrokes-1c96>

*关注 [@learnvim](https://twitter.com/learnvim) 了解更多 vim 技巧和窍门！*

在过去的 1.5 年里，Vim 一直是我的首选编辑，但我仍在学习一些新东西。最近，我试图了解 vim 的点(`.`)命令。

点命令就像一个微型宏。重复上次所做的更改。如果使用正确，它可以节省我们做重复性工作的时间。

我也很好奇你们是如何使用点命令的——欢迎在下面评论！

# 它是如何工作的？

如果您在 vim ( `:h .`)中看到帮助部分，您将看到:

> 重复最后一次更改，用[计数]替换计数...

我马上想到，“vim 说的‘改变’是什么意思？”

经过一些阅读和实验，我得出结论，改变意味着任何更新、增加或减少文件内容的行为。四处走动不算改变。让我们通过一些应用程序来看看这是否是真的。

## 例 1:添加；到每一行的结尾

这里有一个例子([来源](https://www.reddit.com/r/ProgrammerHumor/comments/7uyafj/roses_are_red_violets_are_blue/))。假设我们想在每一行的末尾添加`;`，这可以在`.` :
的帮助下完成

```
Roses are Red
Violets are Blue
Unexpected '{'
On line 32 
```

Enter fullscreen mode Exit fullscreen mode

假设我们从左上方 R 的位置开始。我们从`A ; <esc> j`开始。

1.  `A`跳到行尾并进入插入模式。
2.  `;`加`";"`，回到正常模式，然后下去。

酷，整个(`A ; <esc> j`)就是一个变化，对吧？不。如果我们做了`. . .`，我们会以
结束

```
Roses are Red;
Violets are Blue;;; <-- what happened?
Unexpected '{'
On line 32 
```

Enter fullscreen mode Exit fullscreen mode

这是因为 vim 没有把**而不是**算作`j`变化的一部分。变化不包括运动。在这种情况下，vim 认为变更是`A ; <esc>`。我们需要做`A ; <esc> j . j . j .`。点，下，点，下，点，下。

## 例 2:删除特定单词，但不是全部

例如，假设我们的诗是这样写的:

```
Roses are Red Blue
Violets are Blue
Unexpected Blue '{'
On line Blue 32 
```

Enter fullscreen mode Exit fullscreen mode

我们需要删除除了第二行的蓝色。我们可以使用点命令非常快速地完成它。

`/ Blue c i w <backspace> <esc>`删除第一个蓝色。然后`n n . n .`

这一次，我们的变化包括:

1.  删除整个单词 Blue 并进入插入模式(`c i w`)
2.  在插入模式下退格
3.  出口

我开始看到一种模式。vim 不认为`/ Blue`和`n`是变化，但`c i w <Backspace> <esc>`认为是变化。

让我们再举一个例子:

## 例 3:添加(在每行的开头

另一个，假设你有:

```
One)
Two)
Three) 
```

Enter fullscreen mode Exit fullscreen mode

我们在每个单词的开头加上()。您可以做`I ( <esc>`来首先应用更改，然后再应用`j . j .`。这里的变化是`I ( <esc>`。

## 比较什么‘变了’

让我们比较一下过去几个例子中所有可重复的变化:

1.  `A ; <esc>`
2.  `c i w <Backspace> <Esc>`
3.  `I ( <esc>`

你看到模式了吗？它们都是以进入插入模式的命令(c、A 和 I 都是导致进入插入模式的命令)开始，以`<esc>`结束。

另一个我没有提到的是类似`dd`的删除命令。我可以通过`dd . . . .`反复删线。虽然 dd 没有进入插入模式，但是 vim 认为这是一个更改，因为它删除了一整行。请记住，任何添加、删除或更新文本的操作都被 vim 视为变更。

# 申请

以上是点命令的一些应用。它可以为我们节省一些击键次数——节省一些击键次数就等于节省了时间。下次我们做重复的任务时，看看你能否用点命令重复它。

感谢阅读！我真的很感激你能走到这一步。黑客快乐！

# 资源:

*   [实用 Vim](https://www.amazon.com/Practical-Vim-Thought-Pragmatic-Programmers/dp/1934356980)
*   [为什么点(。)命令在 VIM 中这么有用？](https://stackoverflow.com/questions/7325052/why-the-dot-command-is-so-useful-in-vim)

# 讨论

我很想知道其他开发者是如何利用 dot 命令的。你认为还可以用点命令的其他方式吗？