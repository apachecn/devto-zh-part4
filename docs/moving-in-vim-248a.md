# 在 Vim 中移动

> 原文：<https://dev.to/camilo/moving-in-vim-248a>

# 在 Vim 中移动

每个尝试过 Vim 的人都知道移动文件的不可靠的`hjkl`键。这些通常会让新用户感受到我们需要的两种情绪之一:愤怒和困惑。

[![A scene from the sitcom The Good Place where a character says "You humans have so many emotions! You only need two - Anger and Confusion!"](img/06cf8456070faf9e8343ecc2a607cd63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2tr7FA7T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://camilopayan.com/img/thegoodplace-anger-confusion.gif)

如果我告诉你，有一种方法可以让你的代码像天鹅跳水一样流畅，像思考一样快，同时让编辑变得轻而易举，那会怎么样？这就是 Vim 运动发挥作用的地方。

Vim 移动是向某个方向移动光标的命令。`hjkl`键都是移动命令的例子。在他们的例子中，向左、向下、向上和向右的运动。Vim 还有其他围绕文本对象以及您想要进入的方向构建的动作。如果你想进入下一个代码块，而不是多次按下`j`键，你可以点击`}`进入下一个空行，在下一个代码块之前着陆(为了可读性，你把代码隔开了，对吗？).用一个`}`交换几次点击`j`按钮既节省时间又节省手指。它帮助您以最快的速度到达代码中需要的位置。

通过在命令模式下运行`:help motion.txt`，您可以在 Vim 帮助文件`motion.txt`中了解所有可用的 Vim 运动。

如果我们只是消除击键，它已经值得你花时间去学习了。但是 Vim 运动是可组合的。这意味着他们可以将移动与 Vim 的编辑操作结合起来，对文件进行修改。

让我们回到关于移动到新代码块的例子。如果您想删除下一个代码块之前的所有内容，该怎么办？我们已经知道移动到下一个空行是`}`，所以我们可以将它与删除命令`d`结合起来创建`d}`。这将删除光标所在位置的每一行，直到下一个空行。

现在你不仅移动得更快了，而且编辑文件的速度也更快了！

动作和命令不仅仅是 Vim 和 Neovim。Vim 仿真层，比如 JetBrains IDEs 的 IDEAVim 插件也有动作。对所有动议的支持可能并不总是有的。例如，我上一次使用 PHPStorm 的经历意味着在一些动作上的失败。我错过了那些动作，于是我又开始使用 Vim。

正常模式下的 Vim 动作和命令是 Vim 的语言。当你学会说这种语言时，你会发现你可以比你想象的更快地浏览文件。