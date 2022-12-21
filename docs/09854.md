# 游戏开发后期:高塔

> 原文：<https://dev.to/wrldwzrd89/game-development-postmortem-tallertower-bi2>

什么是正确的:游戏从塔的一层到另一层的流动，保持额外的研磨最小化，如预期的那样工作。

哪些事情做得不对:当然，你不需要磨洋工，但是你花了太多时间寻找出路，以至于你得到的钱比你花的钱还多。怪异的地牢布局也于事无补。

我的一个更有趣的实验是用“Diane”做的，这是一个自制的 Java 语言游戏引擎——用于许多其他游戏。我甚至为它们做了一个家谱...

```
  [ROOT]
    |
    |----------------------------------------------------------------
    |                                                                \
Maze Runner ---------------------------------------------            |
    |           \               \                        \           |
Fantastle  Dungeon Diver    LaserTank (original)  Maze Runner 2  TAP (Text Adventure
    |           |               |                                    Parser)
 Mazer5D   Dungeon Diver 2  LaserTank Remix
    |           |               |
BrainMaze  Dungeon Diver 3  LaserTank EE (Enhanced Edition) (inactive)
    |           |
GoldHunt   Dungeon Diver 4 --------------------------------
                |                         \                \
           DDRemix / DD5                Katie     Dynamic Dungeon (new)
                |                         |                |
           Dynamic Dungeon (old) / DD6  Rachel    Twisted Trek (inactive)
                |
              Gemma
                |
            TallerTower (focus of this post)
                |
        Chrystalz (inactive)
```