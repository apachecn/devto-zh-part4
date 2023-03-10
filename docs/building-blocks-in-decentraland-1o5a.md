# 分散土地上的建筑块

> 原文：<https://dev.to/decentraland/building-blocks-in-decentraland-1o5a>

### 场景内内容创作的简单实验

在 9 月 16 日开始的下一次 Game Jam 之前，6 月黑客马拉松的参与者将接管分散博客，并透露他们的设计和建造秘密。本周的客座博主是 Interweaver(又名[诺亚](https://share.decentraland.org/avatars/email%7C5d234157027519401391c79d)，在世界上)。

嘿大家好。此处交织。白天，我建立互动教育网站，但也对在线多人游戏有着长期的兴趣。这可以追溯到童年时代，那时他在《江湖》中砍树，在《第二人生》中通宵达旦地写剧本。

像我们许多人一样，我在 2017 年底遇到了分散的土地，非常兴奋地终于看到真正的点对点、无信任的虚拟世界的梦想开始实现。内容创作和社区绝对是任何元宇宙的核心，所以我抓住机会参加了最近的 Creator 竞赛和 SDK Hackathon，以此来真正了解 DCL 的新构建工具。

#### 玩积木

作为社区黑客马拉松的参与者，我参与了几个令人兴奋的项目，包括生态游戏区的教育回收游戏。在这篇博文中，我将讲述我是如何设计和实施我的[分散块](https://buildingblocks.noah7.now.sh)项目的。这个场景的想法很简单:我希望能够在实际站在场景中时直观地构建东西，而不是在单独的构建器中或使用外部文本编辑器和建模软件(就像使用 SDK 一样)。此外，我不是 3D 艺术家，所以它必须完全由原始形状制成。

在真正的黑客马拉松中，这一幕发生在大约晚上 8 点和第二天早上 5 点之间(工作日*—*)。最终结果有点粗糙，但达到了我的目标。该场景允许您:

*   通过单击从一堆不同的积木中抓取一个新的积木
*   只需移动和环顾四周，就可以随身携带
*   通过再次单击放置承载块
*   通过单击拾取任何放置的块
*   通过将块放置在地板上的符号上来删除块
*   挑选新积木的颜色
*   选择块移动模式:
    *   携带模式，在这种模式下，积木试图与你保持固定的距离
    *   光线模式，在这种模式下，方块会沿着你的视线移动到最近的固体物体

[![Alt Text](img/b25ccfadcc712ae944622fcccd56da0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--54mFe5Sw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ereg6ers1w0jlptgx69i.png)

[![Alt Text](img/c9d4accede4bd17e6ad03a58008d8424.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IYQAC1Yq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fc3qd99ixt9l1ke1c5b3.png)

为了使排列积木变得容易，它们捕捉到半米的网格，并且它们被阻止交叉，就像真正的积木一样😁。

总的来说，这个场景有点《我的世界》的感觉，但是大部分的积木都比格子大，而且是放在你面前，而不是放在库存里。按照我的主要目标，你完全可以在不离开现场的情况下制造一些有趣的东西。

请随意[亲自尝试一下](https://buildingblocks.noah7.now.sh)！

[![Alt Text](img/0ff53d92dc96a7af041b603d86d78ea9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9a7bdcnO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fa1amcfrfpvmxpy06gp4.png)

[![Alt Text](img/63d925d1874956717fbc1a622996c036.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lDip8KAe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/whbodbawt1obaz4kaj04.png)

[![Alt Text](img/91a3657bc329008b26783e6f0c5f08c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gvsNUphG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uvpbd7qq9i0xakh1l9mp.png)

#### 实现分散块

好了，既然介绍完了，那就来说说它的工作原理吧！如果你想自己查看代码，[都在 GitHub](https://github.com/NoahWarnke/DCLBlocks) 上。注意:为了保持数学的直观性，我有时只描述 2D 的情况，但是 3D 的情况是等价的。

#### 自由进位

场景的核心机制是能够拿起某样东西，带着它到处走，然后在一定程度上精确地把它放回原处。现在，制作一个好的 3D 定位系统可能很棘手。幸运的是，在分散的土地上行走和四处张望已经提供了很大程度的空间控制，所以我决定利用这一点使搬运物品变得容易。我实施的项目的第一部分是免费运输。简单地让一个物体相对于你的屏幕保持在它原来的位置，同时你四处走动并旋转相机，没有任何捕捉或交叉预防或任何东西。

一旦你理解了*世界空间*和*本地空间*之间的区别，这背后的数学非常简单。

**世界空间**是全球 X，Y，Z 坐标系。在分散式场景中，+X 在创世纪城市地图上朝着增加地块 X 坐标的方向前进，+Y 在垂直向上的方向前进，+Z 在地图上朝着增加地块 Y 坐标的方向前进。(我认为这令人困惑，并希望他们选择对齐 Y 坐标并使 Z 垂直，但事实就是如此)。当你根据这个坐标系描述一个物体的位置时，你给出了它在世界空间中的位置。

**相比之下，局部空间**是相对于某种变换(位置、旋转和缩放的组合)的坐标系。它仍然有 X，Y，Z 坐标，你可以通过给出这三个数字来描述物体在局部空间的位置，就像在世界空间一样。但是实际值会有所不同。你通常说的是特定物体的局部空间；例如，**用户空间**是给定用户的本地空间。是用户的相机在原点**T6【0，0，0>T5】的空间，+X 方向是沿着用户的视线，+Y 方向是用户屏幕上的向上方向，距离与世界空间中的相同(比例= 1)。**

有了这个认识，免费携带就真的好定义了。如果 **ObjPosWorld** 是你想要携带的物体在世界空间中的位置(**粗体**表示一个[向量](https://www.mathsisfun.com/algebra/vectors.html))，并且 **ObjPosUser** 是它在用户空间中的位置，你只需要 **ObjPosUser** 在物体被携带时保持不变！这将导致当用户四处移动时，对象在用户的屏幕上看起来没有移动。请注意，如果用户四处移动， **ObjPosWorld** 将不会保持不变。我们这里的目标是计算 **ObjPosWorld** ，这样我们就可以使用[系统](https://docs.decentraland.org/development-guide/systems/)在每一帧上更新它，从而保持 **ObjPosUser** 不变。这将涉及世界空间和用户空间之间的转换(两个方向)。

当对象第一次被拾取时，我们知道 **ObjPosWorld** 并想用它来获得 **ObjPosUser** 。数学很简单。让我们把 **UserPos** 作为用户的位置(在世界空间中)，把 **UserRot** 作为一个[四元数](https://www.euclideanspace.com/maths/algebra/realNormedAlgebra/quaternions/)来表示他们相机的旋转。四元数听起来比它们更可怕——你可以简单地把它们想象成一组四个数字，可以代表任何可能的三维旋转。

如果你把一个矢量乘以一个四元数，它实际上给你一个新的矢量，这个新的矢量是旧的矢量，但是旋转了四元数所代表的旋转。也可以取一个四元数的逆或共轭，得到一个新的四元数，表示旧四元数的反向旋转。从世界空间中的位置( **ObjPosWorld** )转换到用户空间中的位置( **ObjPosUser** )时，我们只需要知道这些信息:

**ObjPosUser**=(**ObjPosWorld**-**UserPos**)*共轭( **UserRot** )

基本上，你是从对象的位置减去用户的位置，然后从对象的位置反转用户的旋转。最终结果是对象的本地用户空间坐标。

作为一名程序员，当有人为一个算法写出一堆数学，而把代码留给读者作为练习时，我总是感到沮丧，所以让我们避免这种情况😁。下面是上面的等式在 TypeScript 中的样子，使用了分散的 SDK，其中 entity 是你携带的对象:

```
// Run this once, when you first pick up the object.
let objPosWorld = entity.getComponent(Transform).position

const objPosUser = objPosWorld
  .subtract(Camera.instance.position)              // Subtract UserPos
  .rotate(Camera.instance.rotation.conjugate())    // Unrotate by UserRot 
```

现在我们有了常量 ObjPosUser，我们只需要使用它，以及最新的 UserPos 和 UserRot 值，在每一帧重新计算 ObjPosWorld，并将携带的对象移动到那里。请记住，当用户四处移动时，UserPos 和 UserRot 会发生变化。这是与上面相同的等式，但是求解的是 ObjPosWorld 而不是 ObjPosUser:

**物镜** = ( **物镜** * **UserRot** ) + **用户**

或者，在代码中:

```
// Run this once per frame, in order to move the object as the player moves.
let objPosWorld = objPosUser
  .clone()                           // (Clone stops the .rotate from changing objPosUser)
  .rotate(Camera.instance.rotation)  // Rotate by UserRot
  .add(Camera.instance.position)     // Add UserPos

entity.getComponent(Transform).position = objPosWorld 
```

此外，作为一个视觉思考者，当有人写下一堆文字并留下图画作为读者的练习时，我总是感到沮丧，所以让我们也避免这样做。下面是这种情况的示意图(在这种情况下，用户恰好直视该对象):

[![Alt Text](img/f0887b06918fbdb10a883749e4306b39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2JqMaw44--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h3nszvp7tsz0tubdkdbb.png)

这基本上涵盖了免费运输。我在我的 GitHub 上做了一个简单的、无依赖性的实现，作为一个组件和系统对(T1 ),还有一个小小的示例游戏(game . ts )( T3)展示了它的用法。

### 对齐捕捉到

我为这个场景做的一个设计决定是将块位置以半米为增量对齐。这使得对齐块更加容易。我最初的实现基本上很简单，在实际更新对象的位置之前，对 objPosWorld 变量的每个组件调用 Math.round()，但是先乘以 2，然后再除以 2，四舍五入到最接近的 0.5m 而不是 1m:

```
objPosWorld.set(
  Math.round(objPosWorld.x * 2) / 2,
  Math.round(objPosWorld.y * 2) / 2,
  Math.round(objPosWorld.z * 2) / 2
) 
```

现在，用户可以随身携带积木，它们的位置是离散的，便于放置！但是块仍然可以在彼此内部移动——这是一种非常不现实的情况。

### 检测重叠部分(或网格)

下一个挑战是检测一个位置何时已经被占用，并防止移动块到那里。实现这一点的一个自然方法是拥有一个大型多维数组，我称之为网格，其中每个元素代表单一地块场景中的一个 0.5 立方的单元(因此是一个 32x32x32 的数组。)然后你简单地:

*   在移动方块之前，检查方块将占据的每个网格元素
*   如果它们都是空闲的，移动该块并在网格中将它们标记为被占用
*   如果用户再次移走该块，再次将它们标记为未被占用
*   如果它们不都是免费的，我们将需要以某种方式解决重叠(见下文)

这种机制的一个很好的副作用是，当我们在读取数组之前检查数组索引以确保它们在范围内(0 到 31 之间)时，如果它们不在范围内，我们也可以在这些情况下防止重叠，并避免块进入地下或超出我们的地块边界。

[![Alt Text](img/0b6309a44412f87348863906531b7338.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pHBaJD2o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vjgq8zroom89lje3v30n.gif)

### 解决重叠

那么，当用户试图移动一块积木，并提示重叠时，该怎么办呢？简单地不移动块是一种选择，但是会感觉对用户没有反应。一个更好的解决方案是将方块移动到另一个**未被占据的位置，这个位置仍然沿着用户的视线**。这意味着该块看起来会像预期的那样移动(保持在用户的正前方)，但也不再与另一个块或场景的边缘重叠。

### 用 Bresenham 算法栅格化

为了实现这一点，我需要一些方法来确定沿着用户视线的所有可能的位置。谷歌了几分钟后，我偶然发现了 [Bresenham 3D 算法](https://www.geeksforgeeks.org/bresenhams-algorithm-for-3-d-line-drawing/),[将 3D 空间中两个整数点之间的一条线段](https://en.wikipedia.org/wiki/Line_drawing_algorithm)光栅化；也就是说，它将线段转换成一组沿着线段的**体素。我当然可以使用其他的算法，但是这个算法已经足够满足我的需求了。你可以查看回购协议，看看我的[类型脚本实现](https://github.com/NoahWarnke/DCLBlocks/blob/master/src/carryable.ts#L204)。**

[![Alt Text](img/3db3dc0246b2203a1f43da632b921ef9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zjJwKHZ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6hrt4cpf3yy8h9x9kxuk.gif)

### 使用栅格化

有了沿用户视线的体素集，我们现在可以解决重叠。如果你还记得，有两种不同的块移动模式，进位模式和射线模式。

在携带模式下，当你走动和环顾四周时，积木会尽量与你保持固定的距离。当检测到重叠时，只需遍历体素集，使用网格检查每个点的重叠，从最远的*体素(进位位置)和向内移动的开始。你遇到的第一个可用点是你移动方块的地方。这可确保挡块尽可能靠近运载位置。*

[![Alt Text](img/0ebed974e8f5d1b9ab00e89a25429f1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x5CGAksO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tk8m70qtx2f09fovvn8j.gif)

在光线模式下，该块在不碰到任何东西(另一个块或场景的边缘)的情况下尽可能远，然后停在那里。所以简单地遍历体素集，再次使用网格来检查重叠，但是这次从最近的体素(用户的位置)和向外移动的开始。你遇到的第一个不可用点之前的最后一个可用点就是你移动方块的地方。这确保了块就在用户视线上的第一个实体的正前方。

[![Alt Text](img/89f718c811f3cee21046a7f5db609265.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z0axVOmz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1pc59g61gn9slpg2d0cv.gif)

### 总之

差不多就是这样。我们可以通过走路和环顾四周来自然地携带积木，它们可以捕捉到一个网格以便于放置，并且它们不能彼此相交。对于一个九小时的项目，我对结果相当满意。这无疑向我展示了，虽然分散化的 SDK 仍处于发展的早期阶段，但你已经可以用它完成很多事情了。

我期待着许多新功能即将推出！特别是，如果全局点击事件可用的话，分散块将会少很多错误并且更容易编写，而不是总是需要点击一个实际的对象，这个特性是为 SDK v6.3 或更早版本计划的。

展望未来，我期待成为分散式场景构建社区的积极成员。9 月份将会有另一个[分散式游戏大会](https://gamejam.decentraland.org/?with=interweaver),根据我对过去的黑客马拉松的经验，从对 SDK 一无所知开始，我可以确定这是一个很好的机会让你熟悉它，同时与一些真正熟练的创作者一起工作！

我计划参加，如果你对为一个去中心化的网络世界构建内容感兴趣，我希望你能加入我。

元宇宙见！