# 战斗引擎开发日志#3 -回合制战斗

> 原文：<https://dev.to/matt_parke/battle-engine-devlog-3-turn-based-combat-35i3>

现在我们开始冒险了，战斗引擎的下一个核心部分是搜索一个怪物战斗的区域。你和你的朋友可以轮流使用技能来击败怪物产卵。

当考虑战斗将如何进行，当怪物出生时人们将如何与它互动时，基于回合的流程最有意义。以下是做出回合制决策时需要考虑的一些问题:

*   角色如何被允许攻击怪物？
*   我们如何防止在文本频道中发送垃圾命令？
*   在小组中，每个人如何轮流表演？
*   我们如何衡量怪物受到攻击时的反应？
*   怪物一次攻击所有人还是一个人？
*   怪物会等到所有人都完成攻击还是对玩家角色做出反应？

在战斗中，每个角色都有机会使用一项技能，在角色回合之间，怪物有机会做出反应。

要与你的队伍开始战斗:

```
.beng fight 
```

一个怪物将诞生，你现在在战斗中！

[![](img/58061cb5ae8583eb84203e28857b4d69.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fLd1IyKp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6qzmhmooxjb3cuf3yfxc.png)

接下来，我们可以*使用*技能，通过命名我们想要使用的蛞蝓。对于新手来说，所有新手角色的主要攻击技能都是一记重拳。

```
.beng use punch 
```

[![](img/48afe92728e39821e0b93b94f44b8e95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p5eM8c_v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ule6nt676yae8an7hlp.png)

哎哟！看来野猪用头撞技能反击了。每次攻击都会根据所选技能的最小/最大伤害范围计算伤害，并以剩余的生命值作为回应。

再打几拳，我们就能打败野猪了！

[![](img/28f772c5452c256400afc57b9182ed10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d24wdwJb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vo5wr6rv5gzz0xmypkw3.png)

战斗结束时，无论输赢，都会显示一个总结。如果结果是一个胜利，总结包括一些分配给团队所有成员的经验。但是如果你输了，你的角色将会丧失能力，你需要在再次战斗前回家。截至发稿时，你不太可能会输掉一场战斗，因为技能加成和技能效果互动仍处于早期阶段。

就这些了，来和我们打个招呼吧！[加入战斗引擎不和](https://discord.gg/cG2ReFR)。

想帮忙或报告一个错误？查看当前问题，并在 GitLab 上为 [Battle Engine 投稿。欢迎所有技能和经验水平，无论是内容写作，艺术，发展，或只是想法！](https://gitlab.com/kamajigames/battle-engine)