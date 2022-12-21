# 疯狂游戏开发博客#1

> 原文：<https://dev.to/nuculabs_dev/mad-game-devlog-1-3en3>

大家好！

在这个博客中，我会尽力让你了解我们游戏的最新状态。因为我们都有全职工作，所以我们只在空闲时间开发这个游戏。

我一直在执行世界生成代码，因为我没有任何可用的墙壁精灵，所以我用无菌脚本制作了自己的精灵。

[![](img/94a3cb2dca945a2aacf7d8955b721e4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kx-W0FHv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x9to3149mg2foghf89sn.png)

使用上面的精灵，我在 Unity 中创建了两个预设，我把其中一个预设的颜色改成了棕色，生成的世界看起来像这样:

[![](img/e8eb25e4a9e7c646f9814f7afc81c801.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bTDq43MY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f9xknt4gbwcrr7qp83th.png)

玩家精灵也是我做的。

几天后，我从 Max 那里收到了墙壁精灵，我把它们导入了 Unity。

[![](img/3de5aaf2cdd75928cb0859ed3d47fca3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zXMFG1Rj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gxp0vxnpdc63m1k4145o.png)

精灵看起来这么小的原因是 Max 设计了它们，所以它们可以重叠，但我们不确定我们将如何做到这一点，因为这个游戏是基于矩阵网格添加重叠的精灵可能会使它有点混乱。

[![](img/8059a59628a13a73a9cec1e2b99859f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8h-UMEqY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ofa11ul3irl4nygzshbe.png)

这里有一些图片，展示了游戏重叠后的样子。

[![](img/9fa8737cc5488364fd8bd6c180089fc1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Q8b_fp2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jxohuosgcd2jb9sfhjm7.png)

[![](img/11ecd9ff7c366de8b7d48b002c888761.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fTMxLfal--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lj23ruge3z3eyrcdd2jj.png)

安德烈承担了让炸弹工作的最繁重的任务。他在 Unity 的光线投射、协同程序和 2D 物理系统中苦苦挣扎。过了一段时间，他设法让它工作起来。炸弹已经放置，爆炸已经产生，它仍然需要一点润色。

[![](img/0a689a2066de1aa7ccf6b6786e0b6eec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NpmICGcu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bfzq62uy8gqhppqc0nph.png)

感谢您的阅读，祝您愉快！

[![](img/6523318639e71ace85958c8f9712875f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x2HPH2qb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4t30ab747l8bp7kwzi3q.gif)