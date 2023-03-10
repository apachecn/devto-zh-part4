# 了解如何向现有构建器场景添加基本的交互性

> 原文：<https://dev.to/decentraland/learn-how-to-add-basic-interactivity-to-an-existing-builder-scene-l8m>

在 9 月 16 日开始的下一次 Game Jam 之前，6 月黑客马拉松的参与者将接管分散博客，并透露他们的设计和建造秘密。本周的博客嘉宾是 Tak。你可以用@Tak 找到不和谐或松弛的 Tak。

嘿，我是塔克张全欣，我是一个三维艺术家。在这个行业中，我是你们所说的通才，因为我几乎涉猎了计算机图形管道的所有领域。

事实上，我是从一个朋友那里知道分散土地的，他建议我去看看，也许我会感兴趣。事实证明他是对的。从那以后，分散地成了我的新游乐场。

在 6 月的黑客马拉松中，我创建了一个叫做 [Kabloom Farm](https://kabloon-farm-babylon.now.sh/) 的东西，它在地区类别中获得了第二名。我会在 9 月 16 日回来参加比赛，这次是为了一等奖。

### 简介

[Builder](https://builder.decentraland.org/) 为任何人提供了一种简单的方法来设计场景，而不必担心创建自己的艺术资产，然而它的真正优势在于你可以导出这些场景以供进一步开发，利用分散的 SDK 中可用的所有工具和实用程序。

让我们从一个使用构建器创建的简单花园场景开始。它有一个固定的大门，这意味着用户最终可以进入花园，而不必跳过围栏。通过使用新的 [Utils](https://github.com/decentraland/decentraland-ecs-utils) 库，我们将很快能够响应用户的点击打开和关闭大门，并且希望在本教程结束时，你将能够看到任何人都可以用最少的努力创建一个简单的交互场景。

点击查看本教程[的最终代码。](https://github.com/takJohn/adding-functionality-to-a-builder-scene)

### 该设置了

通过在终端中运行以下命令，确保安装了最新版本的分散式 SDK:

`npm install -g decentraland`

您还需要从这个[链接](https://github.com/takJohn/adding-functionality-to-a-builder-scene/blob/master/assets/Pavilion_Stone_Fountain_-_Tutorial.zip)下载构建器场景文件。下载完成后，将它们解压缩到自己的文件夹中。

请随意使用您自己的构建器场景进行跟进。只要从构建器本身下载你的场景，你就会得到一个类似的*。zip* 文件。您可以将以下步骤作为通用指南。

### 奔跑的场景

在终端内部，在我们提取构建器场景的目录中运行以下命令:

`dcl start`

这将安装任何缺失的依赖项，并自动打开默认浏览器预览场景。这可能需要几秒钟来加载，但一旦完成，你应该会看到一个亭子坐落在一个小花园里，周围有木栅栏。

[![image1.jpg](img/38bfb5c5c9f0d3381cdd76e3687e5a53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--13QwTePQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.steemitimages.com/DQmNM66UU26YfzuDSuy54xcjVqXbTiyUo9qvwvKYniwQhRy/image1.jpg)

如前所述，用户必须跳过栅栏才能进入花园，所以我们的目标是当用户点击大门时，大门可以打开和关闭。

### 转换物品

让我们从整理大门开始，让它填满栅栏之间的缝隙。我们可以在代码中做的事情之一，目前在构建器中是不可能的，是缩放项目。为了实现这一点，我们将通过执行以下操作来修改门的`Transform`组件:

*   打开位于`/src`目录中的`game.ts`
*   向下滚动直到找到`fencePicketDoor_01`或者只搜索该术语
*   修改属于门的`Transform`组件

提示:来自构建器的代码可能会很长，因为场景中的每个项目都会生成一个新的代码块。为了更容易地在代码中定位一个项目，您可以返回到构建器，将鼠标悬停在您希望在“项目目录”中搜索的项目上，工具提示将弹出，为您提供项目的名称，它应该与代码中使用的名称非常相似，因此您可以在搜索它时使用它作为提示。

```
/// --- Adding Basic Interactivity to gate ---
const fencePicketDoor_01 = new Entity()
fencePicketDoor_01.setParent(scene)
const gltfShape_16 = new GLTFShape('models/FencePicketDoor_01/FencePicketDoor_01.glb')
fencePicketDoor_01.addComponentOrReplace(gltfShape_16)

// Scale and position the gate
const transform_31 = new Transform({
  position: new Vector3(6.65, 0, 0.5),
  rotation: Quaternion.Euler(0, -90, 0),
  scale: new Vector3(1, 1, 1.5)
})
fencePicketDoor_01.addComponentOrReplace(transform_31)
engine.addEntity(fencePicketDoor_01) 
```

你可以在上面的例子中看到，我们将`scale`调整到在这个场景中适合我们的值。如果你正在处理你自己的场景，你可能需要尝试一下，看看什么适合你。简而言之，大门的位置和规模已经调整，以适应围栏之间的空间。

我们还用等效的[欧拉](https://en.wikipedia.org/wiki/Euler_angles)替换了[四元数旋转](https://en.wikipedia.org/wiki/Quaternions_and_spatial_rotation)值，这在稍后旋转闸门时将会很重要，至少在某种意义上，我们将能够从概念上更好地理解它。

> 提示:当从一种度量转换到另一种度量时，这个在线工具是一个很好的帮助。

### Utils library

是时候给场景添加一些功能了，为此，我们将使用新的 Utils 库，它包括许多预建的工具，可以简化向场景添加常用功能的过程。

如果您尚未退出预览服务器，请在安装 Utils 库之前退出，这可以通过在终端的场景项目文件夹中运行以下命令来完成:

`npm install -g decentraland-ecs-utils`

要将库导入场景脚本，请在`game.ts`文件的开头添加这一行。

```
import utils from ".../node_modules/decentraland-ecs-utils/index" 
```

我们将只使用这个库中的一些可用助手，但是如果你有兴趣了解更多，那么就点击这个[链接](https://github.com/decentraland/decentraland-ecs-utils)。

### 切换状态

我们将使用的 Utils 库中的第一个组件是`ToggleComponent`，它允许我们在两种可能的状态之间切换一个实体，并在这样做的时候执行一个功能。状态由`ToggleState`指定，可以是`On`或`Off`。以我们的闸门为例，当`ToggleState`为`On`时，我们将打开闸门，当`ToggleState`为`Off`时，我们将关闭闸门，因此每次状态改变时，闸门将旋转到与这些状态之一相对应的位置。

`ToggleComponent`接受两个参数:

*   `startingState`:拨动开关的启动状态(`On`或`Off`)
*   `onValueChangedCallback`:每次切换状态改变时调用的函数

看到门开始关闭，我们将把`startingState`设置为值`ToggleState.Off`。对于切换的第一个测试，在第二个参数上，我们将添加一个非常简单的函数，每当状态改变时，该函数将向控制台记录一条消息，描述我们想要执行的操作。这样，我们可以在执行打开和关闭闸门的操作之前，检查开关是否按预期工作。

*注:每当看到`utils`。这表明我们正在从 Utils 库中访问一个助手。*T3】

```
// Toggle gate to its open / close positions
fencePicketDoor_01.addComponent(
  new utils.ToggleComponent(utils.ToggleState.Off, value => {
    if (value == utils.ToggleState.On) {
      log("Open")
    } else {
      log("Close")
    }
  })
) 
```

### 点击行为

到目前为止，当你点击 gate 时什么也没有发生，我们希望它在被点击时切换状态。我们将添加一个`OnClick`组件到 gate 中，该组件具有一个功能，可以在每次点击时切换状态。

```
// Listen for click on the gate and toggle its state
fencePicketDoor_01.addComponent(new OnClick(event => {
  fencePicketDoor_01.getComponent(utils.ToggleComponent).toggle()
})) 
```

由`OnClick`组件执行的函数在`On`和`Off`之间来回改变`ToggleState`。虽然你看不到场景中发生的任何变化，但如果你打开浏览器的控制台，你应该会看到每次点击大门时消息在`Open`和`Close`之间交替出现。

提示:访问浏览器控制台的方式因浏览器而异。对于 Chrome，你可以使用快捷键 CTRL + SHIFT + J(在 Windows 上)或者 CMD + OPTION + J(在 Mac 上)。你也可以通过视图>开发者> JavaScript 控制台的菜单打开它。

### 旋转闸门

值得注意的是，当门最初放置在构建器内部时，它已经在 y 轴上旋转了`-90`度。之前我们将旋转的`Quaternion`值转换为它的`Euler`等价值，这就是我们现在的开始旋转。对于我们的最终旋转，我们将需要再旋转`-90`度，这样它向内打开，使其最终的 y 轴值为`-180`度。让我们定义几个变量来反映这些值，我们称它们为`startRot`和`endRot`。

```
// Define start and end rotations for the gate
let startRot = Quaternion.Euler(0, -90, 0)
let endRot = Quaternion.Euler(0, -180, 0) 
```

为了帮助我们旋转门，我们将使用 Utils 库中另一个名为`RotateTransformComponent`的组件，它允许我们在一段时间内将实体从一个方向旋转到另一个方向。

`RotateTransformComponent`接受三个参数:

*   `start` : `Quaternion`开始旋转
*   `end` : `Quaternion`为末端旋转
*   `duration`:旋转的持续时间(秒)

我们现在可以删除用作占位符的`log`语句，而是让`ToggleComponent`在每次状态改变时向 gate 添加适当的`RotateTransformComponent`。当门打开时，我们用`startRot`和`endRot`按预期设置开始和结束旋转参数，但是当门关闭时，顺序颠倒，这意味着我们的`endRot`现在是我们的开始旋转，`startRot`是我们的结束旋转。

```
// Toggle gate to its open / closed positions
fencePicketDoor_01.addComponent(
  new utils.ToggleComponent(utils.ToggleState.Off, value => {
    if (value == utils.ToggleState.On) {
      fencePicketDoor_01.addComponentOrReplace(
        new utils.RotateTransformComponent(startRot, endRot, 0.5)
      )
    } else {
      fencePicketDoor_01.addComponentOrReplace(
        new utils.RotateTransformComponent(endRot, startRot, 0.5)
      )
    }
  })
) 
```

在这里，我们用`0.5`秒的时间来打开和关闭大门，这感觉很快，但我鼓励你尝试这些值，这样你就可以选择任何感觉正确的东西。

### 点睛之笔

你可能已经发现了一个小问题，当你在门旋转完毕前再次点击它的时候。它不是在打开和关闭状态之间平滑转换，而是迅速回到其起始位置并再次开始旋转。一个简单的解决方法是，在门旋转完成后，只注意门上的点击。这可以通过比较门的 y 轴旋转来确定它是否与`startRot`或`endRot`匹配。

提示:添加一个中间变量有时很有用。虽然不是必需的，但它可以帮助缩短您的代码，并使其更具可读性。

```
// Listen for click on the gate and toggle its state
fencePicketDoor_01.addComponent(
  new OnClick(event => {
    // Adding an intermediate variable
    let doorRotY = fencePicketDoor_01.getComponent(Transform).rotation.y

    // Check if gate is at its start or end positions before toggling
    if (doorRotY == startRot.y || doorRotY == endRot.y)
      fencePicketDoor_01.getComponent(utils.ToggleComponent).toggle()
  })
) 
```

### 最后的想法

试着运行场景。现在一切都应该如预期的那样工作了。如果你只是在阅读，现在想要探索这个场景，那么你可以在这里找到它。

[![image2.jpg](img/3e4b7db3116103fff57314af3812c07b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gAlBnxI9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.steemitimages.com/DQmY3i4ejkkbXqcNoN2ZQ8YQN7SjcAB9ibaCV1rJU34Jtn7/image2.jpg)

使用构建器在场景中导航和定位项目比纯粹通过代码来做要直观得多。希望本指南已经向您展示了当构建器和 SDK 一起使用时的可能性，以及即使是一点点交互性也可以大大增加场景的趣味性。

在不久的将来，构建器资产将被优化，以在分散的土地内更快地加载，这为那些希望与构建器一起开发他们的场景的人提供了另一个好处。

### 让比赛开始吧

分散式游戏大会提供了一个玩 SDK 的绝佳机会。参加过六月的黑客马拉松之后，我可以向你保证这是最好的学习方式之一，不仅仅是因为你可以做点什么，还因为你可以和其他社区成员一起做。

我已经报名参加了 9 月 16 日的开球，我期待看到你们的成果。

元宇宙见！

### 参考文献

*   [分散土地的建造者](https://builder.decentraland.org/)
*   [分散的土地实用程序库](https://github.com/decentraland/decentraland-ecs-utils)
*   [分散式土地的 CLI 和 SDK 安装指南](https://docs.decentraland.org/getting-started/installation-guide/)
*   [设置实体位置](https://docs.decentraland.org/development-guide/entity-positioning/)
*   [分散的 SDK 的文档](https://docs.decentraland.org)