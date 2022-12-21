# 一个游戏设计文档(GDD)教程，将节省你的时间和精力

> 原文：<https://dev.to/garrett/a-game-design-document-gdd-tutorial-that-will-save-you-time-and-energy-238n>

*注:这是来自[GarrettMickley.com](https://garrettmickley.com)的旧内容。我不再追求游戏设计职业，但是[我不想浪费](https://dev.to/garrett/why-i-m-switching-careers-to-coding-19g8)的内容，所以我把它重新安置在这里。希望 Dev.to 社区觉得有用。*

我在家乡的一所州立大学获得了游戏设计学位。在我们的游戏设计课上，课程非常严格地要求我们每个游戏都有一个游戏设计文档(GDD)。教授们对它的组织方式没有太严格的要求，但是对它应该包含的内容和如何更新有规定。

一旦我从大学毕业，开始自己设计游戏，我就抛弃了这一切，找到了更好的方法。

如果你以前做过游戏，你就会知道最初的想法并不总是成功。有时你最终会改变你的想法，因为原型不能正常工作，或者游戏测试人员直截了当地说它“不好玩”(这就是为什么我强烈建议尽早进行原型和游戏测试...稍后将详细介绍)。然后，你要么放弃这个游戏，要么从你已经完成的工作中寻找有用的东西。无论哪种方式，在此之前构建并重写 GDD 都将是对时间的巨大浪费。

问题是，我们不能完全摆脱 GDD。需要有一个书面的(或打字的，或绘图的，或音乐的)计划，关于游戏中会有什么，它会做什么，它会去哪里。

所以我开发了一个更短，更容易维护的 GDD，我做了一个模板，下面我将和你分享。

## 规则

首先，我们来讨论几个规则。

1.  使用类似 Google Drive 的东西。您可以将所有内容保存在一个文件夹中，并链接文档，此外，您的整个团队可以随时访问最新版本。
2.  这个 GDD 严格坚持游戏设计本身，不包括任何商业方面的东西，如营销。我用一个商业计划来完成所有这些。
3.  没有图像/视频/媒体。把它们放在一个文件夹里，并链接到它们。这样文档就很容易编辑，没有人会因为添加了文本或其他什么而花时间重新排列图像。
4.  如果做了更改，请“另存为…”并将其命名为“GDD_2”、“GDD-3”等。

## 下一页

### 第一页

#### 从名字开始

说出游戏的名字。如果你愿意，你可以用一个工作头衔。我通常直到开发后期才想到游戏的名字，但是游戏从第一天起就需要有一个某种类型的名字。

**例如:**

> 加勒特的游戏商品好游戏

#### 添加你的团队名称

添加您团队所有成员的姓名。等级和排名不是必须的，只要把它们按照对你有意义的顺序排列就可以了。

**例如:**

> *   siedah garrett
> *   Tom
> *   Ollie
> *   Willie

### 第二页(以及从那里开始的所有页面)

#### 电梯间距

你应该为你的游戏准备一个电梯广告。电梯间推介是对游戏进行简要描述的一句话，但要足够详细，让人们能够决定他们是否会感兴趣。

**例如:**

> 它是曲棍球、保龄球和沙狐球的结合，但在触摸屏上是二维的。(用于我的游戏，我讨厌红色方块)

#### 故事简介

一个简短的，最多一段的故事梗概。如果这个游戏是一个有着宏大故事的史诗，那就把它放在另一个文档里。如果你的游戏没有故事，那你就做错了。每个游戏都需要一个故事。如果你的故事只有一句话长，那没关系。

**例如:**

> 史蒂夫(玩家)在一个荒岛上醒来。他除了身上的衣服，什么都没有，眼前是一大片物质。他用这些材料制造工具、庇护所和探险。每当夜幕降临，怪物就会来袭击他。他竭尽所能以求生存。

#### 玩法特色

这是你描述玩家如何玩游戏的地方。包括将涉及的所有特征。将每个特性分解成自己的小部分，这样每个人都知道每个单独的东西是什么，这样如果需要更改或删除什么，就更容易找到。在这部分包括游戏控件(A 代表跳跃，D 代表移动，等等)。

**例如:**

> 玩家将在屏幕上点击他们的手指，并向他们点击的地方发送蓝色圆圈。想法是点击屏幕顶部的红色方块。红色方块会在屏幕上来回跳动。目标是让玩家用手指尽可能少地敲击屏幕顶部的所有红色方块。有时候会出现灰色方块，是不可移动的障碍物。

#### 级别(如果需要)

不是所有的游戏都有关卡，但是如果你的有，你会需要这个部分。否则，就跳过它。

如果您确实需要，包括级别号、名称(如果合适)和级别描述。像以前一样，确保每个都在自己的部分，以防需要编辑或删除。

**例如:**

> *   class A
> *   -darkness. Players must go through the darkness and find a flashlight. Players must look for flashlights in the room after waking up. They can't leave until they leave the room. When the player finds the flashlight and approaches the bedroom door to leave, the level ends.

#### 菜单屏幕

描述每个菜单屏幕以及其中需要的所有内容。大多数游戏至少有一个主菜单屏幕、一个暂停菜单屏幕和一个设置菜单屏幕。很多游戏也有关卡菜单，人物设计画面等。这些都很重要，要列出来。确保每一个都有自己的小部分，这样如果需要修改或编辑，就可以很容易地找到并修改。

**例如:**

> *   primary menu
> *   -Three buttons (level selection, setting, exit)
> *   Grade selection
> *   -There is a list of all 15 levels.
> *   set up
> *   -Option to turn music and sound effects on or off.

#### 需要艺术资产

在这里，您将包括所有需要的资产以及简要描述。我将由艺术总监与制作这些资产的艺术家讨论艺术的细节。不要忘记，每个级别，区域，菜单等都需要有艺术，所以这可以在这里分解。

**例如:**

> *   class A
> *   -A blue circle.
> *   -A red square.
> *   -Left wall
> *   -Right wall
> *   -Bottom
> *   -Background
> *   -pause button

#### 需要健全的资产

这就像上面的艺术资产列表，但你也列出了你需要的声音。

**例如:**

> *   class A
> *   -Knock.
> *   -The sound of circles hitting squares.
> *   -The sound of diamonds hitting diamonds.
> *   -The sound of a circle hitting the wall
> *   -The sound of diamonds hitting the wall.
> *   -The box leaves the screen.
> *   -The sound of the circle leaving the screen
> *   -Background music
> *   -pause button sound

就是这样！这就是组装一个易于修改的坚固的 GDD 所需要的全部内容。

这是我为自己制作的模板，我在所有的游戏中都使用它。去下载吧。免费的！