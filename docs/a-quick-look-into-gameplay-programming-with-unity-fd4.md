# 用 Unity 快速浏览游戏编程

> 原文：<https://dev.to/scottslatton/a-quick-look-into-gameplay-programming-with-unity-fd4>

自从今年 5 月从 Flatiron 学校毕业后，我已经把很多精力放在了我个人的爱好上:游戏开发。我学习 C#是为了在 Unity 中编写脚本，Unity 是一个 3D 游戏开发程序，我在大学时就学会了如何使用它。然而，在我的训练营经历之前，我从来没有被正确地教过如何编码，所以我可以说我的眼界得到了前所未有的开阔。现在我想和你分享我在过去几个月里学到的一些东西。

让我们直接跳进来吧！

### 什么是组件？

在 Unity 中，有些代码可以耦合到单独的对象上。这些代码被称为组件，因为它们是模块化的，可以附加到许多不同类型的游戏对象上，以增加功能。您可以将组件想象成一个球窝系统，在这个系统中，组件可以非常顺畅地与其他组件进行交互。

当你在 Unity 中开发游戏系统时，你基本上是以一种内聚的方式将这些组件放在一起，这样它们就可以相互交流，为玩家创造一个有趣的游戏循环。Unity 使用 C#来编写脚本，作为这些对象上的组件，因此您不仅可以创建一个简单的健康变量，还可以创建一个可再生的健康系统，通过简单的拖放就可以应用于任何东西。

[![Component Example](img/2ee6b30f5ee90c5237c24891f2d78cb6.png "Component Example")](https://res.cloudinary.com/practicaldev/image/fetch/s--FTkggQZi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/q0JGG48.png)

### 获取用户输入

让用户输入动作其实很简单。在 Unity 中，你只需要访问[输入类](https://docs.unity3d.com/Manual/class-InputManager.html)并通过字符串指定你想要访问的输入。

[https://repl.it/@ScottSlatton/OvercookedRudeLivecd?lite=true](https://repl.it/@ScottSlatton/OvercookedRudeLivecd?lite=true)

### 使变量在检查器内可用

您可以通过在变量声明前使用“Public”标志，使脚本内部的变量可以从检查器外部访问。

正如你在上面的脚本中看到的，我们已经在检查器中设置了移动速度。

[![Accessible Variable Example](img/a1c8ec3b45ffc6e65f13dcb2d3470209.png "Here Move Speed is available to change in the Inspector")](https://res.cloudinary.com/practicaldev/image/fetch/s--L9m5MaxR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/YONqP9Q.png)

我们不仅可以利用“Public”关键字使整数、浮点数和字符串可访问，我们还可以使组件及其属性可访问。

### 碰撞

碰撞是艾萨克·牛顿爵士介入的地方，并给我们的游戏增加了一些有趣的物理学。你可以从刚体碰撞力学中构建整个游戏(参见愤怒的小鸟)。像 Unity 中的许多东西一样，碰撞器是组件，特别是刚体和碰撞器。

来自 [Unity 文档:](https://docs.unity3d.com/Manual/RigidbodiesOverview.html)

*一个**刚体**是使**游戏对象**能够进行物理行为的主要组件。附着刚体后，物体会立即对重力做出反应。如果还添加了一个或多个**碰撞器**组件，则**游戏对象**会被即将到来的**碰撞**移动。*

另一方面，对撞机是实际上相互碰撞的无形边界。

因此，如果我们希望我们的游戏对象受到物理的影响，我们需要一个刚体组件和一个碰撞器。如果你没有碰撞器，物体将能够穿过其他物体。如果你缺少一个刚体，这个物体将不会受到物理的影响，但是会用碰撞器排斥其他物体。这是一个重要的特点，因为我们可以让我们的层面，像地面和关闭的门，对我们的球员不可逾越。

这些是我们可以在 Unity 中利用的一些工具，来创建人们喜欢玩的游戏。下一篇博文，我将讨论触发器、光线投射和不同脚本之间的交互。