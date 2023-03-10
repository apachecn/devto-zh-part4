# 用 Godot 制作游戏

> 原文：<https://dev.to/triptych/game-making-with-godot-j7d>

我假期的一部分时间花在了一个名为#ittybittyrpg 的新游戏制作工具上。我正在 [Godot](https://godotengine.org/) 中构建它，并取得了许多重大进展。这里有一个最近的保存和加载工作的截图，以及一个来自 [Logomakr](https://logomakr.com) 的 logo

[![](img/7b1733dbf39c629d933a5aa94aaf369c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YDQdL_z---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.snap.as/gwZdNEu.png)

我对 IttyBittyRPG 有一个非常有趣的想法，不是像我最初想做的那样做 Twine 或 Bitsy 之类的事情，这个想法更像是你有一个幻想世界贴纸书，你可以创建对话，战斗和关卡，就像你可以在贴纸书里放贴纸一样。编辑器的用户界面将会是“有趣的”,然后游戏本身将会从中产生。

因此，由于这应该是关于编码的，我不得不将旧版本的 [GDScript](https://docs.godotengine.org/en/3.1/) 的保存/加载代码移植到 3.1。

这里的代码示例是[这里的](https://codeshare.io/alEElB)