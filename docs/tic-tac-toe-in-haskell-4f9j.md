# 哈斯克尔的井字游戏

> 原文：<https://dev.to/dwayne/tic-tac-toe-in-haskell-4f9j>

# 简介

在这篇文章中，我将展示我的经典游戏[井字游戏](https://en.wikipedia.org/wiki/Tic-tac-toe)的 Haskell 实现。

我称之为 [xo-haskell](https://github.com/dwayne/xo-haskell/) 。

我不会说太多的细节，但是我会试着从 10，000 英尺的角度来看这个游戏的整体设计。

# 万英尺视野

游戏被分解成一个[库](https://github.com/dwayne/xo-haskell/tree/139ce51c2a11c55e40bd059468d142edf7f07a1d/src/XO)和一个[可执行文件](https://github.com/dwayne/xo-haskell/tree/139ce51c2a11c55e40bd059468d142edf7f07a1d/cli)。

## 图书馆

库的明星是[游戏](https://github.com/dwayne/xo-haskell/blob/139ce51c2a11c55e40bd059468d142edf7f07a1d/src/XO/Game.hs)模块。它封装并强化了井字游戏的游戏逻辑。

举个例子，假设我们想开始一个 O 先参与的博弈。我们可以这样做:

```
>>> import XO.Mark
>>> import XO.Game as Game

>>> let game0 = Game.new O 
```

Enter fullscreen mode Exit fullscreen mode

`game0`是一个[游戏](https://github.com/dwayne/xo-haskell/blob/139ce51c2a11c55e40bd059468d142edf7f07a1d/src/XO/Game.hs#L80)，其中 O 先玩。

实际播放时，您可以用想要标记的位置调用`play`函数。

```
>>> let Right game1 = Game.play (0,0) game0
>>> game1
o........; x 
```

Enter fullscreen mode Exit fullscreen mode

你不要把 X 或 O 传递给函数，因为轮到谁是在幕后管理的。这样做可以确保游戏总是在 X 和 o 之间交替进行。

你不能在界外或标记的位置打球。

```
>>> let Left error = Game.play (3,3) game1
>>> error
OutOfBounds 
```

Enter fullscreen mode Exit fullscreen mode

在这里强制执行。

```
>>> let Left error = Game.play (0,0) game1
>>> error
Unavailable 
```

Enter fullscreen mode Exit fullscreen mode

在这里强制执行。

游戏结束条件由[裁判](https://github.com/dwayne/xo-haskell/blob/139ce51c2a11c55e40bd059468d142edf7f07a1d/src/XO/Referee.hs)模块检查。它导出了一个名为[unsafecide](https://github.com/dwayne/xo-haskell/blob/139ce51c2a11c55e40bd059468d142edf7f07a1d/src/XO/Referee.hs#L40)的局部函数，该函数可以决定玩家何时获胜或者游戏何时被终结。

有一个 [AI](https://github.com/dwayne/xo-haskell/blob/139ce51c2a11c55e40bd059468d142edf7f07a1d/src/XO/AI.hs) 模块实现了传统的[极小极大算法](https://en.wikipedia.org/wiki/Minimax#Minimax_algorithm_with_alternate_moves)。了解它如何工作的最好方法是阅读它的[测试](https://github.com/dwayne/xo-haskell/blob/139ce51c2a11c55e40bd059468d142edf7f07a1d/tests/Test/XO/AI.hs)。

最后，使用 [hspec](http://hspec.github.io/) 对库进行彻底的单元测试。点击查看[的测试结果。](https://github.com/dwayne/xo-haskell/tree/139ce51c2a11c55e40bd059468d142edf7f07a1d/tests/Test/XO)

## 可执行

所有与外界的交互，IO，都发生在可执行文件中。

它使用 [optparse-applicative](https://hackage.haskell.org/package/optparse-applicative) 解析[命令行选项](https://github.com/dwayne/xo-haskell/blob/139ce51c2a11c55e40bd059468d142edf7f07a1d/cli/XO/CLI/Options.hs)，然后决定运行哪个版本的 [orchestrator](https://github.com/dwayne/xo-haskell/blob/139ce51c2a11c55e40bd059468d142edf7f07a1d/cli/XO/CLI/Orchestrator.hs) 。

[非交互式指挥者](https://github.com/dwayne/xo-haskell/blob/139ce51c2a11c55e40bd059468d142edf7f07a1d/cli/XO/CLI/Orchestrator/Noninteractive.hs)管理“计算机对计算机”游戏。[互动指挥](https://github.com/dwayne/xo-haskell/blob/139ce51c2a11c55e40bd059468d142edf7f07a1d/cli/XO/CLI/Orchestrator/Interactive.hs)管理“人对计算机”或“人对人”游戏。

需要注意的关键是，当游戏逻辑作为一个单独的关注点来处理时，每个 orchestrator 的实现变得多么简单。

# 为什么要读代码

1.  如果你已经读过 [LYAH](http://learnyouahaskell.com/) 或者 [Haskell 的书](http://haskellbook.com/)，但是你仍然在设计和实现完整的 Haskell 应用程序上有问题。
2.  学习如何对 Haskell 代码进行单元测试。
3.  学习如何记录 Haskell 代码。
4.  学习如何为回合制游戏设计一个库。
5.  了解如何解析命令行选项。

# 结论

用任何语言(更不用说 Haskell)编写一个设计良好的井字游戏似乎是一个不小的问题。我希望这篇文章和 [xo-haskell](https://github.com/dwayne/xo-haskell/) 的源代码向你展示一种可能实现的方式。

你对设计和实现有什么看法？我很想听听你对如何改进的看法。