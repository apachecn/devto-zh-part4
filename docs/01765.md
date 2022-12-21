# Unity Immediate 和自动播放的艺术

> 原文：<https://dev.to/thegamedevguru/unity-immediate-and-the-art-of-automating-playthroughs-3jc>

如果你喜欢开发难度大的游戏，但自己却不是一个好玩家，那么你将面临一个挑战。

[![Unity Immediate Window: Sample Project](img/0e31b4f362ca58d590862681c0c290a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9V3ysVSl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thegamedev.guru/wp-content/uploads/2019/09/Unity-Immediate-Window-Project.jpg)

上周，当我在做一个游戏时，我发现我再也不能超越自己的水平了。可以说，我想找到最终的老板，这件事花了我很长时间，我都不愿意承认。可能是年龄的原因，或者是这个游戏太难了。

所以我开始寻找不同的方法来节省时间。一如既往，作弊是一种选择。我还想制作一个特殊的，更短的游戏版本，以避免每次都经历相同的困难。毕竟我只是想测试一下最终的 boss！

如你所知，有很多方法可以解决这个问题。但是，对我来说，*我想到了一个替代方法，这个方法非常有用*。这个策略帮助我缩短了游戏的测试时间，并减少了设计迭代时间。

如果您是程序员或设计师，这篇文章将让您了解处理这些挑战的第二种方法。如果你有任何其他角色，那么，把这篇文章分享给合适的人。

为了展示这个用例，我在 GitHub 上找了一个开源游戏。所以我找到了一个可爱的小游戏叫做爆米花，这是我从冰岛借来的(完全归功于他！).这是一个基本的 2d 平台游戏，具有简单的 2d 物理机制，非常适合我们的用例。看上面的截图，是不是很萌？

我将从告诉你我过去如何处理测试开始...

[![Unity Immediate Window: Sample Project](img/0f572db26ab98f0a59e4b963e1be0d36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Sb6T9ACO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thegamedev.guru/wp-content/uploads/2019/09/Unity-Immediate-Window-Level1-Grinding.jpg)

# 一级 Unity 开发者:研

十年前，研磨是我喜欢的工作方式(哦，天哪)。我做的所有测试都是手动的，这很有趣。

然后，几周过去了，游戏变得越来越复杂。我开始变得沮丧，因为现在每次玩都要花 20 分钟。调整最后的 boss 场景从一个愉快的时刻转变为吸干我所有的法力和燃烧整个 QA 的年度预算。

必须做点什么，所以我寻找解决办法。

一个解决方法是*将每一层分成多个甚至更细的子层*。这样，我们可以只加载我们想要测试的特定子层。这种解决方案对于关卡区域高度相互独立的游戏可能很有效，比如平台游戏。当涉及到场景管理时，这对程序员来说有一些开销，但这是很好的。

另一种可能性是添加自定义的*游戏内面板或 UI 检查器来启用作弊*和特殊动作。这些可以让开发者跳过某些部分，增加统计数据或只是禁用恼人的广告。这些工作得很好，但是必须花费一些时间来准备和维护前端。

接下来是经典的:一个在运行时加载的配置文件。这个偷偷摸摸的文件可以用来选择启用哪些功能或欺骗。那可能看起来像下面:

```
{
  "GodMode": true,
  "AllLevelsUnlocked": true,
  "Gold": 500,
  "SkipAds": true,
  "UnlimitedTime": true
} 
```

您可以通过如下 C#脚本加载这样的配置文件:

```
[Serializable]
public class Cheats
{
    public bool GodMode;
    public bool AllLevelsUnlocked;
    public int Gold;
    public bool UnlimitedTime;
}
var cheats = JsonUtility.FromJson(File.ReadAllText(Path.Combine(Application.persistentDataPath, "cheats.txt")));
player.Gold = cheats.Gold; 
```

当然，确保不要在发布版本中启用该特性😉

这些选项有一个共同的问题:*我们必须预先定义什么是可能的*。我们得到我们编程的东西；我们得不到我们没有的。

这很快就会成为时间的沉淀:

在测试过程中，我们决定我们需要 X。因此，我们停止播放，等待程序员实现 X。

在某些情况下，我们显然可以从更大的灵活性中获益。

所以我问自己:我们能做得更好吗？

*...继续阅读 [TheGamedev 上的文章。咕噜](https://thegamedev.guru/unity-immediate-for-automated-playthroughs/)T3】*