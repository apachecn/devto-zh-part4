# 💻我发布了我自己的 Visual Studio 代码扩展包，下面是你也可以这样做的方法！

> 原文：<https://dev.to/lampewebdev/i-released-my-own-visual-studio-code-extension-pack-and-here-is-how-you-can-too-5g87>

# 简介

Visual Studio 代码自带了许多特性，看起来已经很棒了。尽管如此，你还是想让它有家的感觉，像你的家🏡。

为此，您可以安装扩展、主题和图标。人们会忘记他们已经安装了哪些扩展，或者安装了太多的扩展，然后发现卸载它们很痛苦。

也许您的 hole 团队使用 Visual Studio 代码，您希望与您的团队共享您的扩展。这也有助于新同事的入职过程。

您正在使用`Remove Development`扩展，并且想要在您的远程设置上安装许多扩展。

因此，扩展包可以在许多方面提供帮助，让您的生活更轻松、更高效。

# 我的扩展包

基于以上原因，我发布了我的扩展包。LampeWebDev 的扩展包
如果你想拥有相同的主题、图标和扩展，我大部分时间都在使用，安装那个扩展包！改变颜色和图标主题，你就可以开始了！

# 如何创建您的扩展包

## 要求

你需要在你的机器上安装带有`npm`、`git`和`Visual Studio Code`的`nodejs`。这些安装高度依赖于您的操作系统。

## 生成扩展包模板

Visual Studio 代码团队围绕 Visual Studio 代码扩展开发提供了优秀的工具。当您想要开发 visual studio 代码的扩展时，应该使用它。

您需要安装两个 npm 软件包。第一个是 yoeman(NPM 包叫`yo`)，第二个是`generator-code`包。Yoeman 是一个 npm 包，可以帮助你启动新项目。约曼需要所谓的发电机。这些发电机就像是 yoeman 的新 kickstart 选项，你可以调用。

*   安装所需的软件包

```
npm install -g yo generator-code 
```

根据您的设置，您可能需要使用`sudo`或管理员权限运行该命令。

*   不，你可以打电话告诉 yoeman 启动一个 visual studio 代码扩展

```
yo code 
```

*   现在你需要选择`New Extension Pack`。尤曼现在要提问了。你可以在下面的例子中看到它们。

[![](img/6f8c097f905e4c7361310419dfe93125.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MyHRIG1T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qk4nya0lp0k4gnq2jcl3.png)

# 向您的包中添加扩展

## 找出唯一的扩展名

有两种方法可以做到。一种是在浏览器中，另一种是在 Visual Studio 代码扩展选项卡中。唯一的包名由名称和包名组成，用点分隔。`<AuthorName>.<PackageName>`

*   当您在 Visual Studio 代码中打开扩展的页面时，您会在包名称的右侧看到唯一的包名称

[![](img/ac7f26f73befc918bb53ae03d381733f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xAjHf9e7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fxyhio9m5uydqzzu3nn7.png)

*   当你转到[Visual Studio code markt place](https://marketplace.visualstudio.com/)并搜索你正在寻找的扩展，并转到该扩展的详细信息页面时，你可以在地址栏中看到唯一的名称。

[![](img/382a509e26a55bc2fd8e4befc09541e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8nEAZYca--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rnx6gmuutnkuhwk40tsz.png)

## 向 package.json 文件添加扩展名

现在您知道并复制了您的扩展的唯一名称，您需要打开 yoeman 的扩展包的 generate 文件夹中的`package.json`文件。在`package.json`文件中，你需要搜索`extensionPack`键。这个键必须是具有唯一扩展名的字符串数组。

应该是这样的:

```
{  ...  "extensionPack":  [  "robbowen.synthwave-vscode",  "laurenttreguier.vscode-simple-icons",  "visualstudioexptteam.vscodeintellicode",  ...  ],  ...  } 
```

在你的扩展包中添加所有你想要的扩展。

# 运行/测试您的扩展包

我们需要检查我们的扩展是否真的会被安装。为此，我们需要有一个填充并保存的`package.json`文件。你可以点击`F5`或者点击`CTRL+SHIFT+P`并输入`Debug: start debugging`。

您将看到一个新的 visual studio 代码窗口，当您导航到 extension 选项卡时，您应该会看到您的扩展包以及包中引用的扩展。检查是否一切正常。

在 WSL 或 SSh 之类的`Remote Development`会话中，这一步对我不起作用。

## 编辑 README.md 和 CHANGELOG.md

编写自述文件是一种很好的做法。我在这里想做的是创建一个包含所用扩展名的列表，并添加链接。

我创建了一个小模板:

```
# <Name of the Extension>

## Included Extensions

### Theme and Icons
 - [Name of the Extension](LINK)
 - ...

### Generic
 - [Name of the Extension](LINK)
 - ...

### Framework Specific
 - [Name of the Extension](LINK)
 - ... 
```

变更日志不仅有助于让其他人了解最新的变化，也有助于让你记住自己的变化。

下面是我的模板:

```
# Change Log

All notable changes to the "<PACKAGE NAME>" extension pack will be documented in this file.

## [X.Y.Z]
 - Changed something

## [0.0.1]
 - Initial release 
```

不要忘记保存这两个文件。

# 发布您的扩展

现在我们知道你的扩展正在工作，我们需要发布它！

## 需要的账户

你需要一个`Microsoft account for this`。你可以在这里创建一个。

之后，您必须创建一个 Microsoft 帐户，以便导航到发布管理页面。要到达那里，点击[这里](https://marketplace.visualstudio.com/manage)。如果没有为您创建发布者，您需要创建一个新的发布者。您可以再次在两个地方找到您的出版商名称。

[![](img/a4b00938cb0783a86496f2c0cf0ad665.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3r3AzFFR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z9zq2s289p3zzk5j0y2t.png)

打开扩展包文件夹中的`package.json`文件，并将这个发行商名称添加到其中的`publisher`键下。对我来说，它看起来像这样

```
 "publisher":  "lampewebdev", 
```

## 整理好你的`package.json`文件

如果需要，将版本更改为`1.0.0`。

一个图标囊括了一切，你不需要成为一个设计师来创造一个。网上有很多 Logo 生成器。我喜欢的是用[孵化的](https://hatchful.shopify.com/)。创建您的徽标，并将 png 或 jpeg 文件放入您的扩展包文件夹中。您也需要将它添加到`package.json`文件中。

```
 "icon":  "icon.png", 
```

在存储库中在线发布您的包是一个很好的实践。你可以在 Github、GitLab 或 BitBucket 或你的私有 git repo 上这样做，只要它可以通过 git URL 访问。

复制您的 git URL 并打开`package.json`。这里我们需要添加以下条目:

```
 ...  "repository":  {  "type":  "git",  "url":  "<GIT_URL>"  },  ... 
```

## 创建您的`vsix`文件

vsix 是文件扩展名，它指定该文件是 visual studio 代码扩展名，我们现在需要创建该扩展名。

为此，我们需要安装另一个 npm 包。它叫做`vsce`

```
npm install -g vsce 
```

您可能需要`sudo`或管理员权限来全局安装`vsce`。

现在在终端中导航到您的扩展包文件夹并运行以下命令:

```
vsce package 
```

该命令将创建一个新文件。

## 将您的扩展上传到市场

现在我们需要将文件上传到市场。点击[这里](https://marketplace.visualstudio.com/manage/)。点击`+ New Extension`，会出现一个下拉菜单。点击 Visual Studio 代码，将会打开一个模态窗口，您需要将您的 vsix 文件拖放到该模态中并上传。

现在市场会做剩下的事情！几分钟后，您会在版本旁边看到一个绿色勾号。这意味着您的扩展包现在已经成功地向全世界发布了！

你可以点击扩展名称，这将带你到你的扩展的公共包！

就是这样！您现在已经发布了您的扩展包！

# 结论

为你的团队准备一个扩展包是一件有益的事情，你应该考虑为你和你的团队创建一个。

此外，如果您经常更换工作站或在远程环境中工作，这可以让您立即投入使用。

你可以查看我的扩展: [LampeWebDev 的扩展包](https://marketplace.visualstudio.com/items?itemName=lampewebdev.lampewebdevs-extension-pack)
你可以查看这里的代码:[lampewebdevs-extension-pack
T5】](https://github.com/lampewebdev/lampewebdevs-extension-pack)

如果你能为我做以下事情，那将对我有所帮助！
去 [Twitch](https://dev.to/twitch_live_streams/lampewebdev) 给我留个关注！如果只有少数人会这样做，那么这对我意味着整个世界！❤❤❤😊

**👋说你好！**[insta gram](https://www.instagram.com/lampewebdev/)|[Twitter](https://twitter.com/lampewebdev)|[LinkedIn](https://www.linkedin.com/in/michael-lazarski-25725a87)|[Medium](https://medium.com/@lampewebdevelopment)|[Twitch](https://dev.to/twitch_live_streams/lampewebdev)|[YouTube](https://www.youtube.com/channel/UCYCe4Cnracnq91J0CgoyKAQ)