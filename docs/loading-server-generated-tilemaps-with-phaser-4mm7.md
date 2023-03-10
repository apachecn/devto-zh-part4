# 使用 Phaser 加载服务器生成的 Tilemaps

> 原文：<https://dev.to/jorbascrumps/loading-server-generated-tilemaps-with-phaser-4mm7>

Phaser 是一个用 web 技术创建游戏的奇妙框架。内置了许多功能，让创建游戏变得轻而易举。我最喜欢的是 Tilemaps。无论你是开发一个侧滚平台还是一个史诗级回合制 RPG，你都可能需要利用 Tilemaps 来渲染你的关卡。我将简要介绍如何使用它们，然后演示如何利用 API 来动态地提供级别信息。

> 注意:本文包含在 Phaser 中使用加载器和 Tilemaps 的简要入门。如果你已经熟悉了这些概念，请随意[跳到好东西](#loading-server-data)。

## 文件加载器

Phaser 使得使用加载器将各种文件类型加载到游戏中变得非常容易。对于原始文件类型，如图像和音频，以及自定义 Phaser 对象，如 Spritesheets、Plugins 和 Tilemaps，有许多内置加载器。出于我们的目的，我们将把重点放在 Tilemap 加载器上，但是可以在[实验室](http://labs.phaser.io/index.html?dir=loader/&q=)中随意查看所有可能的加载器。

每次调用加载器时，都需要传入一个键和一个文件路径(这是有例外的，但我们假设现在不是这种情况)。该键必须是唯一的，因为它将在以后用于引用加载的资产。下面是一个这样的例子(使用从[平铺](https://www.mapeditor.org)导出的地图):

```
function preload () {
    this.load.tilemapTiledJSON('map', 'data/map.json');
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以根据游戏的需要加载任意多的资源，尽管这会影响你的加载时间。一定要尽可能优化你的资产，这样你就不会让你的玩家等着了。

> 注意:虽然从技术上来说，在游戏中的任何地方加载资源都是可能的，但是我们建议你在场景的`preload`方法中加载所有的资源，因为加载过程会自动开始。如果您选择在其他地方这样做，一定要调用`Loader.start`方法。更多信息，请查看[加载器插件文档](https://photonstorm.github.io/phaser3-docs/Phaser.Loader.LoaderPlugin.html)。

## 创建静态 Tilemap

在我们准备创建第一个 Tilemap 之前，我们首先需要加载一个 Tileset。我们可以扩展我们的`preload`方法来包含我们的 Tileset 资产:

```
function preload () {
    this.load.image('tiles', 'assets/tileset.png');
    this.load.tilemapTiledJSON('map', 'data/map.json');
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备好出发了！

Phaser 又一次为我们提供了惊人的内置支持，支持使用 Tilemaps。可能性是无穷无尽的，但让我们暂时把注意力集中在基础上。在回顾下面的片段时不要眨眼；你可能会错过:

```
function create () {
    const map = this.make.tilemap({
        key: 'map',
    });
    const tileset = map.addTilesetImage('tileset', 'tiles');
    const layer = map.createStaticLayer(0, tileset);
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是在 Phaser 中创建一个基本 Tilemap 的全部工作。首先，我们`make`一个新的 Tilemap(注意对应于我们的 JSON 文件的`key`)，使用我们的图像添加一个 tileset，并创建一个静态层。现在，您应该可以在场景中看到您的地图。

我们已经讨论了使用 Tilemaps 的许多方面，以便获得本文的实质内容。我强烈建议你查看一下[实验室](http://labs.phaser.io/index.html?dir=game%20objects/tilemap/static/&q=)中的完整演示列表，包括如何处理碰撞和多层等问题的示例。如果你特别喜欢冒险，试试[动态磁贴地图](http://labs.phaser.io/index.html?dir=game%20objects/tilemap/dynamic/&q=)。

## 加载服务器数据

有时候你可能不想在游戏中使用单一的地图。也许您希望用户能够从大量可用地图中进行选择，但又不想增加您的包的大小。或者你想定期更新地图，但不想强迫用户每次都下载更新。我们可以通过按需从服务器下载地图来解决这些问题。

还记得我们前面提到的异常吗，我们注意到您必须提供一个文件路径到加载器？事实证明，您不仅限于链接到静态文件。例如，您可以构建一个 API 来返回所需的 JSON。现在让我们来探索一下。

> 注意:我将使用 [micro](https://github.com/zeit/micro) 来快速引导一个 API，但是你可以使用任何你喜欢的东西。

我们需要一个你从未见过的最简单的项目。创建一个新项目，并包含一个静态地图 JSON 的副本。您应该得到一个类似这样的结构:

```
|- index.js
|- map.json
|- package.json 
```

Enter fullscreen mode Exit fullscreen mode

您的`index.js`应该看起来像下面的代码片段。如果你使用不同的设置，那么一定要做同样的事情。此时的想法只是读入静态文件，并随请求返回其内容。

```
const map = require('./map.json');

module.exports = (req, res) => {
    res.setHeader('Access-Control-Allow-Origin', '*'); // You should probably change this

    return map;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，启动发动机:

```
npx micro 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，您应该能够访问您的 API 并获得返回给您的地图数据。接下来，我们需要更新我们的`preload`方法:

```
function preload () {
    this.load.image('tiles', 'assets/tileset.png');
    this.load.tilemapTiledJSON('map', 'http://localhost:3000'); // Be sure to update the URL with your own
} 
```

Enter fullscreen mode Exit fullscreen mode

你应该还能看到你的地图，就像你之前看到的一样。让我们稍微加点料。

## 选择自己的冒险

下一个逻辑步骤是能够根据用户操作加载不同的级别(例如，从菜单中选择一个级别，在击败一个级别后前进到下一个级别)。对于我们当前的设置，更新 API 来支持这一点是微不足道的。让我们更新我们的 API，接受一个级别选择作为查询字符串的一部分。

```
const qs = require('qs');
const url = require('url');

const level1 = require('./level1.json');
const level2 = require('./level2.json');

module.exports = (req, res) => {
    res.setHeader('Access-Control-Allow-Origin', '*');

    const {
        level = 1,
    } = qs.parse(url.parse(req.url).search, {
        ignoreQueryPrefix: true,
    });

    if (level === 2) {
        return require('./level2.json');
    }

    return require('./level1.json');
} 
```

Enter fullscreen mode Exit fullscreen mode

诚然，这仍然是一个相当幼稚的实现，但它展示了基本的概念。我们的 API 现在接受一个级别来加载。目前，我们可以请求的唯一级别是级别 2，因为其他所有内容都将默认为第一个级别。现在，我们如何在游戏中使用它呢？

Phaser 中的场景可以在启动时用数据初始化。然后我们可以存储这些数据以备后用。在下面的例子中，我选择了使用[注册表](https://photonstorm.github.io/phaser3-docs/Phaser.Scenes.Systems.html#registry)来存储数据，但是您可以使用您喜欢的任何方法。

```
function init ({ level }) {
    this.registry.set('level', level);
}

function preload () {
    // ...

    const level = this.registry.get('level');
    this.load.tilemapTiledJSON('map', `http://localhost:3000?level=${level}`);
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:这里不需要缺省值；API 会解决这个问题。

拼图的最后一块是触发水平负载。我们的游戏第一次运行时，存储在注册表中的等级将是`undefined`，因此 API 将知道为我们返回第一个等级。在我们假设的情况下，让我们假设用户已经完成了这个级别，现在显示一个继续下一个级别的提示。

```
this.input.keyboard.once('keyup_SPACE', () => {
    const level = this.registry.get('level');

    this.scene.restart('level', {
        level: level + 1,
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

场景现在将使用下一个层级集重新开始。或者，你可以在游戏结束的情况下将`level`设置为 1，或者甚至在物品拾取时转换到随机奖励等级。

## 结论

传统上，你必须将游戏的所有关卡数据捆绑在一起。有了这种技术，情况就不一定总是这样了。我不认为每个游戏都应该这样设计，但是对于那些有关卡编辑器，以某种方式使用程序生成，或者提供跨设备游戏的人来说，这可能是有用的。

谢谢你抽出时间来和我一起冒险！多年来我一直想试着写一篇文章，但直到现在都没有成功。请在 [Twitter](https://twitter.com/jorbascrumps) 上的评论中告诉我你的想法。