# 不熟悉 VS 代码，不知道如何预览你的代码？看这个！

> 原文：<https://dev.to/dayrentolliver/new-to-vs-code-and-have-no-idea-how-to-preview-your-code-read-this-5ca3>

### 我为什么写这篇文章

我对 VS 代码的主要困扰是不知道如何预览我的代码。在谷歌上搜索了无数次后，我还是没有找到答案。这让我怀疑 VS 代码是否有预览功能。我无法理解为什么有人会使用它，如果他们看不到自己的代码是什么样子的话。

(这种挫折感实际上导致我停止了几个月的编码工作，因为我认为如果我不能解决像这样‘简单’的事情，那么我显然不适合做开发人员。😞)

直到今年早些时候，有人向我展示了如何在 VS 代码中预览我的代码。回顾过去，我现在明白这对初学者来说是多么的不直观。

免责声明:虽然我不认为你有很多使用 VS 代码的经验，但是我认为你以前使用过文本编辑器。还有，我提供的快捷方式是针对 Mac 的。(我确信它们在 Windows 上是相似的。)

### VS 代码阵痛

当我变得“严肃”时(这是我第一次认为自己是严肃的😂)关于成为一名专业开发人员，我开始和一些开发人员交谈，他们告诉我需要开始使用 [VS 代码](https://code.visualstudio.com/)。

因为我以前用过文本编辑器，所以我认为我可以很快学会 VS 代码。另外，每个人都告诉我它有多容易使用。我是说，这能有多难？！

<figure>

[![](img/e78d026c5f3d489a9fd04dd8c93c0256.png)](https://i.giphy.com/media/h2OLfcSKKthRK/giphy.gif)

<figcaption>Obviously, it wasn't easy for me.</figcaption>

</figure>

### 我对初学友好文本编辑器的体验

当我第一次开始学习编码时，我用了一个很快就要废弃的文本编辑器 Cloud9 (RIP🙏🏽).我喜欢 Cloud9 的一点是，我可以写几行代码，点击“预览”按钮，然后立即看到变化。

<figure>

[![Screenshot of my Cloud9 instance](img/978f527dbfc77db056c04d8fe1e32bad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O4z5M4Ni--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l7wrc063pxs61le3yory.png)

<figcaption>My code is on the left and the preview window is just below the clearly labeled Preview button.</figcaption>

</figure>

当我开始在 [freeCodeCamp](https://www.freecodecamp.org/) 项目中工作时，我最终转移到了 [CodePen](https://codepen.io/) 。同样，CodePen 有一个“保存”按钮，也可以让你很容易地查看你的修改。

<figure>

[![Screenshot of my CodePen instance](img/ec33950d92178cef7f17d3cf5ec577c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JEyZ3z72--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ccok060zi8w6c3ar5qw6.png)

<figcaption>My code is on top along with the Save button and the preview window is below.</figcaption>

</figure>

### 直播服务器扩展

有一次，我向一位开发伙伴解释了我对 VS 代码的不满，他亲切地向我展示了如何使用名为 [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) 的扩展来预览我的代码。

你可以从上面的链接下载 Live Server，或者直接进入 VS 代码中的“扩展”标签并搜索它。

<figure>

[![Red arrow pointing to the 'Extensions' tab in VS Code](img/55c8a73f080748b28f3c6581fd76f824.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U4xSuvWs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jntxgdrjburct64npaul.png)

<figcaption>The red arrow is pointing to the 'Extensions' tab in VS Code.</figcaption>

</figure>

搜索并下载 Live Server 后，打开现有的 HTML 文件或创建一个文件。

如果你不确定如何用 VS 代码创建一个 HTML 文件，请看下面的截图。

<figure>

[![Screenshot of creating an HTML page](img/15e227307a986fe93060b7ea86300e42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_nwOFuOq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bs1ieltp6uitzx9g080v.png)

<figcaption>Click 'New File' under the 'Start' section.</figcaption>

</figure>

点击“新建文件”后，你将被带到一个空白页，在这里你可以开始创建你的 HTML 文件。(别忘了，所有的 HTML 文件必须总是以'结尾。html’)

<figure>

在预览 HTML 文件之前，您必须保存它。(Mac 上的命令+S)
[![Screenshot of unsaved page](img/4c949126dee0d94b022fe4a6166e86fc.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--j5tcQ_d0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ictm9ukbrgvz0wtzncpf.png)

<figcaption>If there's a white circle next to your HTML document, that means it hasn't been saved!</figcaption>

</figure>

现在您的文档已经保存，您终于可以使用 Live Server 进行预览了！

要打开 Live Server，请使用快捷键+ Shift + P。您应该会看到一个下拉菜单，如下图所示。

<figure>

[![Screenshot of Live Server menu](img/91f9cad4f1fa11b060a032629941f52d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OSslqQFG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9uy0kqujqeyzsk3obahp.png)

<figcaption>It's very important that you leave the '>' in the search bar, otherwise you won't be able to search for anything!</figcaption>

</figure>

或者，您可以右键单击您的 HTML 页面，然后单击“用实时服务器打开”
[![Screenshot of right-clicking to open Live Server](img/12f254af2008df77a06245899efede23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r-f8HtW4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gdtnwbrq6nve7cx6nlsa.png)

不管怎样，你的 HTML 文档应该会出现。

[![Screenshot of open Live Server](img/c9ab39200522528cb381894fa9d46807.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2RPlvdEi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ya3c3iy7vmjyjevolj7m.png)

[![](img/9eff0cfc477dc1c7ec6e5a75006cb8a7.png)](https://i.giphy.com/media/12NUbkX6p4xOO4/giphy.gif)

我希望这篇教程对你有所帮助。

感谢阅读！