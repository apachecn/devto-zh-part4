# 为什么我不去 ZSH

> 原文：<https://dev.to/jrop/why-i-m-not-switching-to-zsh-4ngd>

最近苹果发布了一个令人惊讶的公告:由于 Bash 的许可问题，他们决定[将 macOS 上的默认 shell 从 Bash 改为 ZSH](https://support.apple.com/en-us/HT208050) 。此更改会影响 macOS Catalina 和更高版本。众所周知，许多开发人员喜欢在舒适的 Mac 电脑上享受类似 UNIX 的开发体验，因此毫不奇怪，在苹果发布声明后，我的 Twitter 上开始充斥着大量 [ZSH 的言论](https://twitter.com/search?q=%23zsh&src=typd)。

许多人正跳槽去 ZSH。

现在，ZSH 在 UNIX shell 领域肯定不是新的了。我已经听说这个 shell 好几年了，为什么我应该换成它。见鬼，我甚至在 2017 年试了一下。我花了大量的时间定制它，并享受它卓越的交互性、可定制性和完成体验。然而，最终我还是切换回了 Bash。你可能会问，为什么我从 ZSH 降级到巴什？有许多因素，但总的来说，最大的原因是我在使用 ZSH 时比 Bash 获得的额外胜利相形见绌，因为我当时正在远程处理那些默认 shells 被锁定到 Bash 的机器。考虑到我使用的大多数机器都已经有了 Bash，并且 Bash 可以使用 [Bash-it](https://github.com/Bash-it/bash-it) 被定制到几乎与 ZSH 相同的水平，我决定统一我的体验，并在任何地方都集中到一个 shell:Bash。

那么，到底是什么问题呢？不要误会我的意思:我喜欢 ZSH，但在这种情况下，我只是想巩固我的努力，定制一个外壳。最后，我几乎拥有了我最初和 ZSH 一起拥有的所有东西，但这次*到处*都有 Bash-it。得分？90%赢。我要了！

## 权衡“采用成本”

你看，对于我们大多数人来说，只要我们在锁定了 Bash shells 的机器上，我们每天都会继续使用 Bash。因此可以有把握地假设——至少对我来说 Bash 不会很快离开我的生活。因此，值得我花一些时间定制 Bash 来满足我的需求。我们甚至可以假设有一种半数学的方式来看待这个问题:

```
CostOfAdopting(X) = CostOfMaintainingStatusQuo + CostOfCustomizing(X) 
```

你看到那个等式中的常数是什么了吗？是的:维持现状。而我的情况是什么现状？定制 Bash。

因此，根据上面的等式，为了让我有动力采用新的外壳“X ”,它必须具有较低的定制成本。

嗯。我想知道那听起来像什么贝壳。

## 输入鱼的外壳

前一段时间，我带着鱼壳进行了一次测试，当我意识到它不符合 POSIX 时，我立即放弃了。对许多人来说，这是一件大事，尤其是在一种情况下:寻找其他脚本文件。虽然我的日常工作流程通常仍然依赖于能够获得一些脚本文件，但我最近已经将 Fish Shell 重新纳入了我的评估流程。我很兴奋地告诉大家，我现在把鱼壳作为我的日常(个人)外壳！

我直接引入了“为什么”,但我还是要声明:在我的案例中，采用 Fish Shell 的成本非常低。零配置现在风靡一时，理由很充分:在这个瞬息万变的时代，从 0-60 *加速到*需要很容易。鱼壳也不例外！在默认状态下，它为*提供了如此多*合理的默认设置:

*   语法突出显示
*   杀手[完成故事](https://fishshell.com/docs/current/commands.html#fish_update_completions)
*   别名:当你有缩写的时候，谁还需要它们？
*   一种更加理智和一致的语言
*   杀手[文档](https://fishshell.com/docs/current/index.html)
*   说真的: [`funced`](https://fishshell.com/docs/current/commands.html#funced) / [`funcsave`](https://fishshell.com/docs/current/commands.html#funcsave)

当然，我不能让 Fish 无处不在，但是它非常容易使用，以至于我愿意把它作为我开发机器上的日常驱动程序。很快我希望写更多关于我的开发设置，以及我已经能够(容易地)对 Fish 进行的定制。

与此同时，在有限接触鱼类期间，以下是我最喜欢的插件:

*   约格布卡兰/费希尔
*   [火柴鱼/太空鱼](https://github.com/matchai/spacefish)
*   [jethrokuan/fzf](https://github.com/jethrokuan/fzf)
*   [jorgebucaran/fish-bax](https://github.com/jorgebucaran/fish-bax)

[1] Fish 缩写在功能上类似于其他 shell 的别名，但是它们在 shell-edit-line 上进行扩展，所以您可以确切地看到它们扩展到了什么。依我拙见，这很棒，因为它消除了别名的“魔力”:尤其是如果另一个开发人员在监视你:你不再需要解释`gc`是`git commit`的缩写。