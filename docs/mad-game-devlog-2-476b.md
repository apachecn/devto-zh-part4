# 疯狂:游戏开发日志#2

> 原文：<https://dev.to/nuculabs_dev/mad-game-devlog-2-476b>

大家好！第二个发展博客的时间到了！

上周，我们实现了透明墙和玩家动画状态机。Max 继续为游戏设计精灵，Andrei 实现了炸弹动画，它正在对一些敌人起作用。下周我将尝试实现并完成 UpgradeManager，这是一个单独的类，它将处理调度播放器的启动。

透明的墙是使用光线投射实现的，墙会向上投射光线，如果它检测到玩家碰撞器，它会将其 alpha 值降低到 50%，当光线没有与任何东西碰撞时，alpha 值会上升到 100%。在下图中，光线投射用红线绘制:

[![](img/9e4c03ea0417f8b8be161a631d766991.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UwpJjv_m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sc4rr403hp26qqv8x99v.png)

当我第一次尝试做玩家动画状态机时，我很快就迷失了方向，很快就放弃了这个想法。我试着用混合树来实现它，希望它能那样工作，但是没有。

[![](img/d16ddfe2c1e8b2fc69475a6fe22a7d97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oBBkVWuV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1t8xd74bxwiks2uswlcl.png)

混合树用于平滑地混合多个动画，当你在 2D 有一个行走/奔跑的动画时，当 X 是-1，Y 是“不小心”时，你想播放“向左走”的动画。为了实现这种统一，我们融合了树木:

[![](img/9a556df20266b85ab37e4917c6e8f983.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LYW34jso--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/36nir5qk9d2bjwfogxdy.png)

这种方法的问题是，当您有 idleLeft 和 runLeft 时，它不起作用，因为当 X 和 Y 都为 0 时，混合树不知道选择哪个 idle。休息一会儿后，我突然想到了解决方案:用状态机代替混合树。你可以从任何状态进入 RunX 状态，但是在你完成 RunX 状态之后，你将转换到 IdleX。

[![](img/5596244adeef69d217cde4437f778522.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ijkLYY4P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bwarto50aapwym8dn9mq.png)

这和预期的一样，但是有一个权衡，你不能在对角线上移动。每个过渡都有一个附加的 AnimationX 或 AnimationY 规则，如果您同时更改 X 和 Y，您会随机跳转到 RunX 和 RunY 动画，使它看起来像一个小故障。

在 Unity 中，X 和 Y 是状态机的自定义参数，不一定是游戏对象的 X 和 Y 位置。您可以调整移动处理代码，将 AnimationX 和 AnimationY 设置为两者中的最大值，并将另一个设置为 0。

为了在这个项目上合作，我们使用 Github 进行编码，使用 Google Drive 进行资产共享。

[![](img/ded6daf1501464f5b1265ef9cc4184a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RSdZWQbC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a333mkmfq80oxre7y4kt.png)

如果你想在你的 Unity 项目中使用 Github，我建议你生成一个合适的[。gitignore](https://www.gitignore.io/) 文件。

感谢阅读！

如果你想知道“疯狂”这个称号是从哪里来的，它只是来自我们的名字:马克西、安德烈和丹尼斯。因为这是我们的第一个游戏，也是我们第一次尝试一起做一些事情，我不知道该取什么名字。

如果你想开始用 Unity 开发你的第一个游戏，我们发现下面的视频讲座很有激励性和帮助性:[https://learn.unity.com/tutorial/your-first-game](https://learn.unity.com/tutorial/your-first-game)