# 我如何在 2019 年在 Vim 中实现“去定义”

> 原文：<https://dev.to/zev/how-i-got-go-to-definition-working-in-vim-in-2019-2ec2>

```
👇👇👇tl;dr 
# .vimrc
set tags=tags
autocmd BufWritePost *.py silent! !ctags -R --python-kinds=-i --languages=python 2&gt; /dev/null &amp;

$ brew install ctags 
```

Enter fullscreen mode Exit fullscreen mode

在过去的两年里，我花了大量的时间在 PyCharm 上，花了一点时间在 Visual Studio 代码上，都是使用 vim 键盘绑定，主要是编写 Python 和 JS。最终，我回到了某种基于终端的 vim:先是 Vim 8，现在是 Neovim。两种 ide 的“vim 模式”都缺少太多的特性，与 vim 相比，大脑到屏幕的速度差异是显而易见的。

## 我从 IDEs 错过了什么

因为我还不是搜索和替换方面的`sed` / `grep`专家，所以我仍然怀念 PyCharm 中用于重命名和重组代码的重构工具。自动导入也是我没有在 vim 中复制的东西。

然而，我最缺少的是“转到定义”，这是我在 VSC 和 PyCharm 中映射到`ctrl-]`的，因为它在 vim 中是默认的。

## 让它工作+避免浪费时间

正如我为使 vim 更符合人体工程学/更像 IDE 所做的大部分努力一样,“去定义”工作比我希望的要花更长的时间。

对于外行来说，虽然 vim 自带 ctrl-]功能，但它实际上并不知道某个东西是在哪里定义的，除非至少有一个`tags`文件，并且您已经告诉 vim 在哪里可以找到它/它们。您可以手动运行`ctags`,但是如果您希望 vim 始终了解项目中所有定义的位置:函数、模块、常量、类、类型等，这可能会令人厌烦。

我发现的自动更新标记文件的第一个选项并不像宣传的那样有效:我试图[像 Pope 先生建议的那样](https://tbaggery.com/2011/08/08/effortless-ctags-with-git.html)设置 git 挂钩，但是不管什么原因，标记文件在提交时从未刷新。避开这个兔子洞！不管怎样，难道你不想让“转到定义”在提交之间也能工作吗？

我在[一个 StackOverflow 回答](https://stackoverflow.com/a/155515/4386191)的评论中发现了一个修改版本，最终在每次保存时都会刷新标签文件。然而，如果没有`python-kinds=-i`,“转到定义”在 MacOS 上没有预期的效果(在 Ubuntu droplet 上很好)。检查我的`tags`文件，它包含导入，这导致我的`ctrl-]`调用只跳转到当前模块的顶部，导入在那里，而不是实体的定义。

## 最终产品+开发者体验

现在，我只需轻轻一击，就可以非常快速地导航到光标下的任何东西的定义。ctags 的这种用法的好处是 1)它在后台运行，永远不会打断你，2)它很快，3)每次保存时都运行。我还没有用 JS 尝试过这一点(将会尝试[这个](https://github.com/artemave/js-editor-tags))，但是对于在基于 Python 的大型项目中跳跃来说，它非常快速和准确。

## 奖金:`gf`

`gf`填补了 ctags 没有覆盖的一个重要空白:它代表“转到文件”。当你的光标在一个文件名上时，在正常模式下输入`gf`,它就会打开它！

## 下一件要尝试的事情:标记依赖关系和 Python 标准库

正如上面提到的，您可以告诉 vim 多个路径中的多个`tags`文件。我并不经常想“去定义”库代码，但是我可以看到它是如何派上用场的。