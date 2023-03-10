# 我爱上约束布局的 6 个理由！！

> 原文：<https://dev.to/gattalraouf/6-reasons-why-i-fell-in-love-with-constraint-layout-3j1l>

这是一篇我决定写的文章，之前我目睹了约束的力量，看到了它的灵活性，并与它提供的伟大选择进行了互动。我刚刚坠入爱河。不仅因为约束布局允许您创建具有平面视图层次结构(无嵌套视图组)的优化的大型复杂布局，也因为它类似于相对布局(这是我在使用约束布局之前更喜欢的布局)根据同级视图和父布局之间的关系更灵活、更简单地处理所有视图，还因为它有 6 个选项:

## 匹配约束(0 dp 的幂):

> 当一个维度设置为“Match_Constraint”或“0 dp”时，默认行为是使视图尽可能地扩展以满足每一侧的约束(在考虑视图的边距之后)。

[![Match_Constraint](img/f5e0ad95a5e44d2b0720b422242f34f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z20jb0iL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/stoz2dtlsez1ee805pt9.png)

这是我最喜欢的功能，它允许你根据不同设备的屏幕大小来控制视图的尺寸。它只是将 Android 开发中的维度处理提升到了一个新的水平。

## 基线对齐

> 将一个视图的文本基线与另一个视图的文本基线对齐。

[![](img/193d5c4e6aba7958176772211f3af26b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iMA6ALA8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z5h34y7grxkhp7286v7d.png)

我不会谈论太多，因为它的定义说明了一切，它允许你根据文本本身调整视图，这太棒了！！！

## 约束到一个准则上

> 有两种类型的指导方针，垂直和水平指导方针，您可以约束视图，并且指导方针对应用程序用户是不可见的。您可以根据相对于布局边缘的 dp 单位或百分比在布局中定位辅助线。

指南功能简化了布局中的一切响应，因为它提供了使用相对于布局边缘的百分比来定位不同视图的可能性，老实说，这是一个很好的选择。

## 被束缚成一个屏障

> 与指导线类似，屏障是一条不可见的线，可以将视图约束到这条线上。除非障碍没有定义它自己的位置；相反，屏障位置会根据其中包含的视图位置移动。

[![Constrain to a barrier](img/ce740f6e0a2045940481d3c709f812aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AxO7ThfF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mty1nwozwqlvqpngzzks.png)

这个很神奇，使用它你可以处理动态填充视图的不可预测的行为，假设你有一个文本视图，只需设置并把它放在栅栏内，然后相对于栅栏定位其他视图，一切都会自动处理。就是这样，不再需要考虑复杂的环节。它的真正力量出现在使用多视图时。

## 用链条控制直线组

> 链是一组通过双向位置约束相互链接的视图。链中的视图可以垂直或水平分布。

[![Different Chain Styles](img/177e9fcc995ee16af579833854bed793.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aN2BFs4y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/znq4r1fq95mrq5a8nzbf.png)

链在单个轴(水平或垂直)上提供类似群体的行为。另一个轴可以独立约束。它提供了一个快速简单的方法来动态链接视图，而不是静态的，它来自水平和垂直两个方向。它有几种类型，如上图所示。

## 圆形定位

> 圆形定位是将一个微件中心相对于另一个微件中心以一定角度和距离进行约束。这允许您在一个圆上定位一个小部件。

[![Circular positioning](img/87625e7ea1307363060685ee730d4c19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fE1B6_Uq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uvvi2mmmfdc8wu0mj5g8.png)

圆形位置在 android 中是一个全新的定位水平，它根据特定的角度将一个视图的中心与另一个视图的中心连接起来，这不仅允许您轻松地定位复杂的视图，还可以通过改变角度来制作动画。

这 6 个特性并不是 constraint layout 所提供的全部，使用也不仅限于我提到的那些。创造性地使用约束布局可以创建非常酷的布局，你所要做的就是释放你的想象力和一些编码…

## 有用链接:

*   [https://developer.android.com/training/constraint-layout](https://developer.android.com/training/constraint-layout)
*   [https://developer . Android . com/reference/Android/support/constraint/constraint layout](https://developer.android.com/reference/android/support/constraint/ConstraintLayout)