# 使用 DevTools 更快地编写 CSS

> 原文：<https://dev.to/websmyth/using-devtools-to-write-css-faster-efp>

写吧。保存。刷新。编辑。保存。刷新。

听起来熟悉吗？如果这是你的 CSS 工作流程，这可能是一个令人沮丧的经历。

一些工具可以缓解这种情况，但它们通常涉及命令行和本地开发。两者都不总是可能或可行的。

如果有一种方法可以在浏览器中实时查看您的 CSS 更改，会怎么样？没有命令行，没有特殊工具，不需要本地开发。

好吧，它已经在这里了，而且已经有一段时间了:浏览器开发者工具。

使用这些工具，我们可以快速地对我们的 CSS 进行更改，实时地看到它们的效果，然后将更改添加到我们的 CSS 文件中。一种经常被忽视的快速编写 CSS 的方法**和**有助于减少你需要做的故障诊断的数量。

## Chromium 的开发工具

本文中的截图和说明来自 Chromium 的 DevTools。这是在 Chromium 上运行的任何浏览器中都能找到的开发者面板:谷歌 Chrome、Brave、Opera、Vivaldi 和微软 Edge(即将推出)。

Firefox 的开发工具也非常优秀。事物可能有稍微不同的名字和位置，但是 Firefox 允许你做同样的事情，这很酷。

## 入门

有几种方法可以启动 DevTools。您可以:

*   右键单击页面上的任何元素，并选择`Inspect`
*   使用快捷键:在 Mac 上使用`[alt] + [cmd] + [c]`，在 Windows/Linux 上使用`[ctrl] + [shift] + [c]`
*   从菜单中选择开发者工具:`View > Developer > Developer Tools`
*   从 Chromium 的“定制和控制”下拉菜单中，选择`More Tools > Developer Tools`

当你完成后，下面显示的面板将会出现，通常固定在浏览器的右边或底部。

[![Image shows a preview of Chromium’s DevTools](img/61bbddf11e84bd382c740a434d3198c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PKjvKpql--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v9y5dt8f38rheeivg4yq.jpg)

## 选择一个元素

在您可以进行更改之前，您需要做的第一件事是选择您想要更改的元素。有几种方法可以做到这一点。

您可以:

1.  右键单击浏览器中的元素并选择`Inspect`
2.  在 DevTools 的 Elements 部分，您可以展开/折叠 HTML，直到找到正确的元素
3.  使用“单击选择元素”快捷方式(Mac 上的`[alt] + [cmd] + [c]`或 Windows/Linux 上的`[ctrl] + [shift] + [c]`)

有时候方法#1 和#3 是不够的。这可能是因为您的目标元素位于 HTML 中另一个元素的下方，或者该元素只是不可点击。

在这种情况下，您需要使用方法 2。方便的是，当你悬停并点击元素时，DevTools 会高亮显示你在浏览器中悬停的内容。

您可以在下面看到突出显示的工作方式:

[https://player.vimeo.com/video/352902468](https://player.vimeo.com/video/352902468)

## 做出改变

一旦在 DevTools 中选择了想要更改的元素，就可以开始了。DevTools 允许我们以几种不同的方式添加或更改 CSS。

你选择的方法将取决于你在做什么。一些添加 CSS 的方法非常适合故障排除，而如果您想在浏览器中编写项目的 CSS，其他方法会更好。

无论您选择哪种方法，您正在寻找的 DevTools 面板都标有 **Styles** 。

### 快速‘n’脏

当你在 DevTools 中选择一个元素时，Styles 选项卡会显示影响该元素的 CSS。在你写的所有 CSS 之上是一个空白框，包含以下内容:

`element.style {
}`

单击此框中的任意位置，可以在 value 中添加 CSS 属性。您在这里添加的任何内容都将作为内联样式添加到元素中，这可以在元素的 HTML 中看到。

如果你想快速试用一些 CSS，看看会有什么效果，这是一个很棒的方法。

[https://player.vimeo.com/video/352908447](https://player.vimeo.com/video/352908447)

### 编辑已有的规则

这与编辑 [element.style](https://cssfordesigners.com/articles/writing-css-faster#quick-n-dirty-element-style) 的原理类似，但它比*少了一点点*。您的 CSS 中的其他内容可能会优先于您在这里写的内容，但这是它应该做的。

[https://player.vimeo.com/video/352908491](https://player.vimeo.com/video/352908491)

### 添加新规则

如果你正在写 CSS 或者调整一个现有的站点，你经常需要写一个新的规则。您可以通过选择样式中的`+`图标来完成此操作。

这恰好是解决特殊性问题的一个绝妙方法。如果您无法使规则生效，但您可以在样式或计算选项卡中看到它们被覆盖(即，它们显示有删除线)，这可以是一种快速找出问题所在的方法。

您可以看到影响规则的其他选择器，然后调整您的 CSS，直到它在浏览器中生效。

[https://player.vimeo.com/video/352908522](https://player.vimeo.com/video/352908522)

### 选择伪类

“样式”选项卡有一个标签为`:hov`的按钮。这允许你触发可能影响一个元素的各种状态，比如一个`a`——它甚至包括最近添加的`focus-within`，这很酷。

一旦您选择了正确的元素并激活了相关的状态，您将能够在 Styles/Computed 选项卡中看到相关的 CSS。伪类也将显示在浏览器中。

例如，如果你的菜单项应该在你悬停在它们上面时变成红色，那么当你选择元素并检查 DevTools 中的`:hover`伪类时，它们就会变成红色。

**NB:** 识别伪类在哪里被应用并不总是容易的。例如，在菜单中，`:hover`伪类可以应用于`li`而不是`a`。如果您正在努力选择正确的元素，也要检查周围的元素——伪类可能已经被添加到它们中了。

[https://player.vimeo.com/video/352908555](https://player.vimeo.com/video/352908555)

## 跟踪变化

过一段时间后，您可能会开始使用 DevTools 对您的 CSS 进行更重大的更改。如果你一次做了几个改变，你会希望定期记录这些改变，并把它们复制到你的 CSS 文件中。

DevTools 通过跟踪您所做的更改使这变得简单。只要您不刷新页面，这些更改将存储在一个地方，所以您不需要记住您已经更改了什么，然后又改回来...

在“控制台抽屉”中寻找标有**变更**的标签。如果没有显示，您可以通过按`Escape`来触发它。

[https://player.vimeo.com/video/352908617](https://player.vimeo.com/video/352908617)

## 计算样式

CSS 继承有时会变得复杂，能够快速看到 CSS 的最终输出是很有用的。如果您使用相对度量单位，尤其如此，在浏览器呈现代码之前，您指定的值(如`em`)不会被计算。

如你所料，DevTools 有一个有用的工具——计算机风格选项卡。这可能已经打开了(它是顶部有盒子模型的列)-如果没有，它位于**样式**选项卡的旁边。

在这里，您可以看到已经指定的任何属性的最终计算值。这可能是字体大小、边距、填充或其他任何内容。

有用的是，您可以展开它们来查看任何被覆盖的样式(由于特殊性或继承性)。您也可以点击这些按钮，在 CSS 文件的上下文中查看它们。

[https://player.vimeo.com/video/352911709](https://player.vimeo.com/video/352911709)

## 保存到本地文件

如果你在本地开发网站(比如在你的电脑上)，你可以更进一步。DevTools 允许你自动保存你对文件所做的修改。

在**自定义和控制开发工具**下选择`Settings > Workspace > Add folder...`然后找到包含你的 CSS 文件的文件夹。

启用后，一个绿色小圆圈会出现在**样式**选项卡中的文件名旁边。这当然是一个有用的特性，尤其是如果您正在从头开始编写 CSS。但是要小心，因为撤销更改并不容易！

[https://player.vimeo.com/video/352908670](https://player.vimeo.com/video/352908670)

## 加成:四处移动元素

最后一个非 CSS 技巧:你可以拖放 HTML 元素。这是一个您可能不经常使用的功能，但有时会很有用。

[https://player.vimeo.com/video/352915133](https://player.vimeo.com/video/352915133)

*本文最初发表于[cssfordesigners.com](https://cssfordesigners.com/articles/writing-css-faster)T3】*