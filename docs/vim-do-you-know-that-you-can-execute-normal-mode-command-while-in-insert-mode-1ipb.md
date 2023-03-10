# 你知道 Vim 可以在插入模式下执行正常模式命令吗？

> 原文：<https://dev.to/iggredible/vim-do-you-know-that-you-can-execute-normal-mode-command-while-in-insert-mode-1ipb>

*关注 [@learnvim](https://twitter.com/learnvim) 了解更多 vim 技巧和窍门！*

在过去的 1.5 年里，Vim 一直是我的首选编辑，但我仍在学习一些新东西。最近我了解了“插入正常”模式，我想分享一下如何使用这种模式在 vim 中更快地编码！

[![typing-300wpm](img/fe6e425929ac4264e30d1e20e8f4c2d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nYh1rPWj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bz9018prk4ltg68i90tu.gif)

非常感谢您的反馈。如果你想出更多使用这种模式节省按键的方法，请在评论中分享！

# 究竟什么是插入-正常模式？

很高兴你问了！

这是插入模式中的一种模式，您可以在其中执行正常模式命令。不用从`insert`->-`normal`->(做正常模式的事情)->-`insert`切换模式，您可以在保持插入模式的同时执行正常的代码命令。

*您只需在插入模式下按下 **`C-o`** 即可。*

如何判断您处于正常插入模式？在插入模式下，您可以在左下角看到`-- INSERT --`指示器。如果我们按下`C-o`，它会变成`-- (insert) --`。

**重要提示**:在此模式下，您只能执行一条命令。在执行了一个普通模式命令后，你又回到了插入模式。

即使有这个限制，这仍然是一个强大的功能。让我们举一些例子来说明我们可以用插入-正常模式做些什么。

# 示例 1:在保持插入模式的同时导航

比方说，在打字时，你想在当前行的开头添加一个单词。使用`C-o 0`跳到行首，添加单词，然后使用`C-o $`跳回到行尾。

也许你在同一行的逗号后面添加了一些东西，只要做`C-o T ,`就可以直接跳到逗号后面。`C-o $`跳回终点。

你可以将它与其他 vim 动作混合使用。我发现`H, M, L, [], ()`对快速导航很有用。

# 例 2:居中屏幕

我正在用 vim 打这篇文章。当我输入这个的时候，我在屏幕的底部。我可以通过做`C-o zz`来定位我当前的位置。

# 例 3:重复字符

说我要打 100 遍`*`。我可以只输入`C-o 100 i * <esc>`(缺点是，这会迫使你离开插入模式)。

# 例 4:删除更快

如果我想从当前位置删除一段文字，比如一个逗号，我只需做`C-o d T ,`

# 接下来是什么？

这些是我们可以利用插入-正常模式的一些方法。

我很想知道其他开发者如何利用正常插入模式。你们认为插入-正常模式还可以用在什么地方？