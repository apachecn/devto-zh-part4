# 疯狂:游戏开发日志#4，主菜单，相机震动和敌人

> 原文：<https://dev.to/nuculabs_dev/mad-game-devlog-4-main-menu-camera-shake-and-enemies-40a2>

大家好！

以下是我们为第 4 期 devlog 所做的工作:

* * *

我们在游戏中添加了一个游戏菜单，它是交互式的，并且不需要任何代码就可以运行。感谢 Brackeys 制作了令人敬畏的教程，这是我们用于菜单的一个:[https://www.youtube.com/watch?v=zc8ac_qUXQY](https://www.youtube.com/watch?v=zc8ac_qUXQY)

* * *

创建主菜单后，我们想让游戏更有活力，我们研究了一种当炸弹爆炸时摇动摄像机的方法。

第一种方法很幼稚，也没有用。我拿着相机的位置，随机移动它，感觉像机器人一样。幸运的是，Unity 为 [Cinemachine](https://unity.com/unity/features/editor/art-and-design/cinemachine) 提供了一个内置的柏林噪声发生器，这是我们用来管理相机的软件包。

柏林噪声是由肯·柏林开发的，它是:

> 一种用于在计算机生成的表面上产生自然纹理的技术，用于电影视觉效果。

Unity 还允许我们通过修改其振幅和频率参数来调整发生器，这使得使用非常方便。

* * *

最后但同样重要的是，我们对敌人不满意，我们对他们的行为仍然有一些错误，但安德烈在那里修复它们。

相反，我做的是尝试在 Aseprite 中制作一些像样的敌人精灵，Cosmigo Pro Motion 在我的 Mac 上无法工作:(。

[![](img/f74d45fa7212da19fb80555627694a2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hXrZvSWU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c2c63qusqinlosn1hlst.gif)

由于我没有足够的实践，我的精灵看起来不太好，我试图让他们尽可能简单，我只用了两个关键帧时，他们的动画。

如果你想开始制作像素艺术，网上有很多免费教程。以下是一些帮助您开始的信息:

*   [https://blog.studiominiboss.com/pixelart](https://blog.studiominiboss.com/pixelart)
*   [https://oni mille . Tumblr . com/archive](https://onimille.tumblr.com/archive)

* * *

感谢您的阅读，祝您愉快！