# 如何构建和发布自己的 VS 代码市场扩展

> 原文：<https://dev.to/itnext/how-you-can-build-and-publish-your-own-extension-to-vs-code-marketplace-2pjl>

[![](img/70125ce94cb7c7f1b3ba57c4a05f211f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7eUCLNcF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9xfk4nlq3xvfg5gtichr.jpg)

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

本文是系列文章的一部分，这是第一部分。

*   [VS 代码片段创建](https://dev.to/softchris/snippets-for-vs-code-3kjh)，包括谁来创作代码片段
*   VS 代码-发布你的(片段)扩展，**我们在这里**

> 在 VS 代码中创建一个代码片段或者一个颜色主题都很好，但是如果你创建了一些非常棒的东西，你想展示给朋友或者更好的给社区呢？为此，我们有 Visual Studio Marketplace。在本文中，我们将构建一个扩展，并把它带到市场上，供人们下载他们的 VS 代码——你很快就可以称自己为扩展创建者了

本文将涵盖以下内容:

*   **脚手架**扩建工程
*   **编写扩展**，在这个例子中，它是关于编写片段的
*   **将**扩展发布到 Visual Studio 市场

好了，我们在我们系列的第一篇文章中创建了一个代码片段[代码片段创建](https://dev.to/softchris/snippets-for-vs-code-3kjh)。

是时候迈出下一步了，让我们与世界分享我们的创造。那么我们如何着手去做呢？嗯，实际上只有三个主要步骤:

当然，这些步骤中的每一步都有很多子步骤，但是不要担心，我会详细地解释它们，所以在这篇文章结束时，你可以像这样站起来庆祝:

[![](img/6a35430f264d6d0182fcfd47238077f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BdI3D3Hx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7loyhifdpxjkh5hw51t0.jpeg)

## 资源

我们将要展示的是如何在给定 Snippets 模板的情况下创建一个扩展，但是您还可以创建更多的扩展，所以这里有一些链接，您可以在阅读完本文后了解更多:

*   [我所学到的建立自己的延伸](https://css-tricks.com/what-i-learned-by-building-my-own-vs-code-extension/)
*   [扩展创作](https://code.visualstudio.com/docs/extensions/overview?wt.mc_id=devto-blog-chnoring)
*   [安装分机发电机](https://code.visualstudio.com/docs/extensions/yocode?wt.mc_id=devto-blog-chnoring)
*   [建筑颜色他们](https://code.visualstudio.com/docs/extensions/themes-snippets-colorizers?wt.mc_id=devto-blog-chnoring)
*   [发布您的扩展](https://code.visualstudio.com/docs/extensions/publish-extension?wt.mc_id=devto-blog-chnoring)

## 脚手架扩建工程

有一个架子工可以让你在一秒钟内完成一个项目。要安装架子工，只需输入:

```
npm install -g yo generator-code 
```

要搭建一个扩展项目，我们只需要键入:

```
yo code 
```

以上将引发一个对话，你需要在其中做出一些选择。让我们展示每个屏幕，看看我们需要选择什么:

[![](img/375800dce3b3ced952415a90fb2b483c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e9SxugpJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wow0u83j71kfnbkueclw.png)

上面我们有很多选择，但是让我们选择`New Code Snippets`

接下来，我们会看到一个对话框，要求我们选择是从文件夹中导入现有片段还是创建新片段:

[![](img/ae432e8cb98a173fae45e6769aa56c26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tTvFLU2Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xfylpmrcym8y85itonpa.png)

我们选择创建新的，因此我们只需点击`return`键。接下来的步骤是给我们的扩展命名。这是它在 Visual Studio marketplace 中的显示方式，所以让我们给它起个好名字，例如:

[![](img/1bf0f3e96fae16e2071f0f945445c351.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nb-p3fD8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sp7pzb3saezik4aaqmpy.png)

之后的问题是`identifier`。我们只是按下了`return`键。这意味着`name`和`identifier`将是同一个东西:

[![](img/26c872008162734565dd2db75ffe001f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3XvLvIab--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/clv4pg2nww0io8xake2h.png)

问题多了，你以为你完了；)?下一个问题是询问您的分机号码。这将出现在用户面前，所以要确保它是有吸引力的，这样他们才会想安装它。但是，您可以在以后更改它:

[![](img/3112e22edd8a093620d2d9f38395d1b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oOtEKJ1o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wmz4n53n0cd6bz9gsimd.png)

下一步是输入`publisher`。现在你可能在想。我对出版商一无所知，它想从我这里得到什么？现在为发布者想一个聪明的名字，稍后我们将创建一个发布者，我们将在 Visual Studio marketplace 中注册，一切都会好起来的:)

[![](img/1d6c99476c151f47a59d02eb75956ac4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p5vsGq-9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vs81y7cg1k255qn22aq8.png)

在上面你可以看到它已经输入了值`chris noring`作为发布者，这是因为我之前已经创建了一个发布者，因为你还没有，现在只需在这里输入一些东西。

对话流中的下一步是输入语言 id，这意味着这个片段是什么语言。让我们输入`javascript`或`typescript`，记住按照说明用小型大写字母输入:

[![](img/994011dd99b3d1abbcd0f4edc58ae8ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P1nVh27D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/71626jdk6arxn8vmcvr6.png)

在这之后，你实际上完成了。它应该列出需要为您的扩展搭建的所有文件，如下所示:

[![](img/e728a4d39b258c549b7f22d0228d569c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LtFTg_Sp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9jkk5bbcffjf66e89f3d.png)

## 延伸项目解剖

好了，现在我们有了我们的项目，让我们看看我们实际得到了什么:

[![](img/1f8812f1b5078d1776169ffdb40566c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Apjp2MYB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2y3ibdzbe60yler3ngs1.png)

我们的项目自上而下包括以下内容:

*   这里是我们创建代码片段的地方，也是我们花费大部分时间的地方
*   **README.md** ，这包含了关于你的项目的信息。您需要更改此处的信息才能将扩展发布到 Visual Studio marketplace。那么我在这里输入什么信息呢？好吧，你应该告诉用户在安装了这个扩展之后，用户可以使用什么命令。如果你能为用户提供所有不同版本的历史记录以及它们包含的内容，那就更好了。这将向用户表明，随着时间的推移，您非常致力于改进扩展
*   **../.yo-rc.json** ，现在这个文件在项目之外，它包含了相当多的发布者名称，不需要修改它
*   **package.json** ，这包含了一些关于项目的元信息。这里重要的是改变`version`属性，并在您更新和重新发布您的项目时增加它，因为您随着时间的推移对它进行了改进。

好了，现在我们对哪些文件是重要的，以及如果需要，我们应该在哪里进行更改有了更多的了解。

## 创建我们的片段

信不信由你，但这是最简单的部分。我们已经在本系列的第一篇文章中解释了如何创建代码片段，这里再次提供链接作为待办事项链接的提示。我们将满足于只显示一些片段的屏幕转储，如下所示:

[![](img/571d81cdcb3629d26e7f2e0b198e50b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QSQ62eyV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/djrpwap59c2ai6oc0zng.png)

好了，现在我们有了一个片段。接下来我们来试试。

## 测试我们的代码片段

要试用一个代码片段，我们需要安装它。要安装它，我们需要做以下工作:

*   **运行**命令`package`
*   从命令窗口安装
*   在合适的文件中尝试使用

好了，让我们从在终端中运行`package`命令开始。等等，我们首先需要一个依赖项来完成这个任务:

```
npm install -g vsce 
```

通过运行上面的命令，我们安装了`vsce`,它安装了我们需要的可执行文件。我们需要可执行文件来运行像`package`和`publish`这样的命令。

### 打包安装

好了，我们已经运行了上面的命令，并准备好`package`，即创建一个安装文件:

[![](img/6b3705a3c640c7cd00556aad6706526e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DpfeOHmK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hwwmidemp34j3yudxmig.png)

啊，直接出错，开始不好还是？嗯，这实际上是很有帮助的，它告诉我们这个扩展的用户应该得到更好的，他们应该得到一个精心设计的自述文件，它将告诉我关于这个扩展的所有信息。所以，让我们来看看 README.md，给它一些爱吧。

[![](img/e56d7cd18b8a9c2a0d7dfd2d1ecfa602.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y_0OhUoN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8q2smb3ffg6gs2x8kann.png)

这里写了很多东西，但你真正需要改变的是顶部的文本，就像`This is the README`。一旦它消失了，我们可以尝试重新运行我们的`package`命令。在编辑自述文件时，简单说一下。我的看法是，给它一个最初的描述并保留一个像`Features`和`Release Notes`这样的标题是有意义的。`Features`在我的脑海中应该描述所有可用的命令以及它们的作用。`Release Notes`应该包含每个版本发布的历史日志和影响，如纠正错误或添加片段。

一旦我们对自述文件满意，让我们再次尝试运行`package`命令:

```
vsce package 
```

这会抱怨我们在`package.json`中缺少了`repository`属性。我们实际上并不需要一个库来完成这项工作，但是有一个库是很好的。我们可以点击`y`继续。现在应该说它已经创建了一个安装文件，如下所示:

[![](img/13d1a866f2dd09bc4093f3e15fa9306a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gd9AwW3D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8g92gtepjz9tfid4gl29.png)

一旦我们有了这样的文件，我们就可以在我们的环境中本地安装我们的扩展了。这将让我们尝试我们的扩展，以确保一切工作如预期。要安装它，我们必须打开一个命令窗口。为此，请转到 View => Command Palette。然后开始输入“VSIX”。它应该向您显示以下内容:

[![](img/fe969e8fec089325fc30edfb4b158f0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uwWiIc98--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oeznvv4wtmfok9bwj02p.png)

让我们选择该命令。应该会出现一个文件选择对话框，提示您需要选择最近创建的`vsix`文件。之后，VS 代码会在你的窗口底部显示一条信息消息，询问你是否想重新加载 VS 代码。点击`yes`之后，你的 IDE 就可以测试了。

### 试试看

我们刚刚将代码片段安装到 VS 代码中。因为这个片段是为 TypeScript 准备的，所以我们需要创建一个`.ts`文件来查看它。因此，让我们创建一个例子`app.ts`，结果应该是这样的:

[![](img/945408d1083d2a7039c2e181907fc73a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WkNuiPx3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c2j88n423linttpropxz.png)

如果你看到上面的内容，这意味着你的代码片段在给自己一个*击掌*的时候按预期工作了。我们离 Visual Studio 市场和开发者名声越来越近；)

## 发布我们的片段

好了，我们现在到了期待已久的时刻。是时候发布这个扩展并开始在 Visual Studio marketplace 的*霓虹灯*中看到你的名字了:)

### 注册发布者

还记得我们在搭建扩展项目的开始时，系统提示我们输入发布者名称吗？好了，现在是实际创建发布者的时候了。那么我们该怎么做呢？

我们需要创建一个与 Visual Studio Team Service [链接到 VSTS](https://dev.azure.com/) 的帐户。一旦我们在这里创建了一个帐户，我们需要得到一个*访问令牌*

#### 访问令牌创建

当我们在终端中使用`vsce`发布我们的扩展时，需要一个访问令牌。我们可以创建一个访问令牌，方法是转到我们的 VSTS 页面，单击右上角的配置文件，然后选择菜单选项“安全”。到达目的地后，我们需要:

*   选择“个人访问令牌”。

*   下一步是选择创建一个新的这样的令牌。

*   给它一个合适的`name`和`expiration date`，这一点很重要，在`organization`下拉菜单中，你需要选择`All accessible organizations`，否则它将不起作用。下一个重要的领域是`Scopes`。这里你可以选择`custom defined`或者`full access`。如果您只是尝试一下，现在尝试使用`full access`,但是确保稍后撤销该令牌，选择`custom defined`,并将其设置为您可以获得的最少权限。通常，你只是希望它能够有`Extensions`和`Marketplace`访问。

因此，在经历了整个访问令牌创建过程之后，我们应该有一个可以复制到剪贴板的访问令牌。

> 请确保将其复制到一个合适的位置，以便以后检索，关闭对话框后，它不会再次显示。

#### 更新令牌

你的令牌迟早会用完数据。当这种情况发生时，重新生成您的令牌并在终端中运行:

```
vsce publish -p <token> 
```

这将使用您的新令牌，以便您下次发布时只需输入:

```
vsce publish 
```

### 发布

好了，最后我们准备运行发布命令。该跑了:

```
vsce publish 
```

这应该会将我们的扩展发送到 Visual Studio Marketplace。在终端中运行该命令，应该会显示如下内容:

[![](img/3a5bd758f45d3708281ac928d1d4cf05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YBs1N2M6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y01iozx10h8xt8trd6hi.png)

### 享受你的劳动成果

Visual Studio 市场更新需要几分钟时间。假设几分钟过去了，让我们转到 Visual Studio Marketplace 的 URL。如果你等不及，去`https://marketplace.visualstudio.com/manage/publishers/<publisher name>`它应该有一个条目与你的扩展名，并说它正在验证你的分机。好了，让我们来看看我们的作品:

[![](img/dc1a24d05895047672408c9e992566fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2JyEvrJd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/272n3r4nhwbkmb3g2tox.png)

你现在应该很自豪。这就是你的扩展，*在所有的荣耀中*，对整个世界开放。让我们通过在 Visual Studio 代码中的 extensions 选项卡下搜索来证明这一点:

[![](img/f152a3a2d98968573a82b947c75698a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xo9PrCn8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vf3dsqklto1rkh0i2md6.png)

这就是了，你输入了你的扩展名，Visual Studio 代码显示给你看。你是你的延伸的骄傲父母。让我们看看详细信息页面，即我们在自述文件中写了什么:

[![](img/e37eae851dd5b49ab68c5b3030f5148d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A5BfEs93--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ujg31djdi0bsnk36pzv.png)

就在那里。这只是开始。现在出去，创建扩展，我知道**你能做到！！！**

### 提升分机

你需要做两件事来改善人们对你的延伸的看法，那就是:

*   添加存储库
*   添加预览 gif

#### 添加存储库

要添加存储库，请在 GitHub 上创建一个。完成后，让我们像这样添加到我们的`package.json`文件中:

```
"repository":  {  "type":  "git",  "url":  "https://github.com/softchris/node-snippets.git"  } 
```

就是这样，它现在将显示在扩展页面上，人们将能够点击进入您的回购，并查看您是如何构建的，如果您想通过提交 PRs 来帮助您

#### 添加预览 GIF

如果你以前下载过扩展，你可能会注意到其中一些是如何通过展示片段的介绍视频变得非常专业的。这个视频实际上是一个 GIF，添加一个 GIF 到减价很容易:

```
![alt text](images/node-snippets.gif "Using the snippet") 
```

注意上面我们是如何引用目录`image`的。它将能够通过查看您的存储库并期望找到那个`image`目录来解决这个问题。因此，这就是为什么您需要存储库集来完成这项工作。没有存储库，没有预览图像。

您的市场现在将看起来像这样:
[![](img/b60b79a728ffe0a64070e51bc9b310c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s-v1Rq5N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3syd04gho34d051etz0e.png)

上面是一个`still image`所以它不会在这篇博文中以动画的形式展示，但它将在现实中发挥作用。

我建议用 Camtasia 或一些免费选项制作所需的视频，然后在完成录制后将其转换为 gif。

## 总结

总之，我们了解到以下情况:

*   使用架子工创建一个扩展项目
*   给我们的项目添加一些片段
*   在本地测试我们的代码片段，以确保在发布前有效
*   **为发布**而设置，通过安装`vsce`并生成`access token`
*   **凝视**我们在视觉工作室市场的美丽创造
*   **Micdropped** ，通过 VS 代码搜索并找到我们的扩展

走出去，向社区展示你的实力。想跟我说说吗？只要发微博到@chris_noring

### 由我摘录

我目前正在维护几个代码片段扩展。欢迎所有反馈:
[我的扩展](https://marketplace.visualstudio.com/publishers/chris-noring)