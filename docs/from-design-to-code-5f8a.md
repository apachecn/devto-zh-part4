# 从设计到代码

> 原文：<https://dev.to/xlayers/from-design-to-code-5f8a>

我们已经有一段时间没有向您更新 xLayers 了。在本文中，我们将介绍 xLayers 以及它可以为您作为设计人员或开发人员的日常工作带来的价值。但首先，让我们重复一下 xLayers 的目标，这将有助于我们理解为什么创建 xLayers。

**目标**:

xLayers 是一款在线网络应用，旨在为设计者和开发者之间架起一座桥梁。它的使命是在设计者和开发者之间建立一个简单的协作世界，这样他们可以快速迭代。

现在我们都已经了解了主要目标，让我们继续，看看 xLayers 是如何工作的。我们在 xLayers 中的旅程将包括三个部分，从草图文件到代码。

## 第一部分，从 SketchApp 到 xLayers:

在我们开始使用 xLayers 之前，我们需要一个“草图”文件。有了这个文件，我们就可以开始使用 xLayers 了！(如果您没有任何草图文件，不用担心，我们提供了几个演示文件)

打开 [https://xlayers.dev](https://xlayers.dev) ，我们会到达登陆页面，在这个页面上你会看到 xlayers 会为你提供的所有东西的各种信息。看看它，现在，我们将继续我们的旅程，如何从草图创建代码。

在登录页面上，您看到一个带有“开始”的按钮，让我们按下这个按钮。

[![xlayers-landing-page](img/a3080cc8dd11178c7995648c950da575.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2M8n5owV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l4m7dsp8oxrg74vjwiex.png)

在这里，我们可以上传我们的草图文件或选择一个演示文件。

[![upload your file](img/5be44d28a430d26018cac5ae417533ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BX1wIQth--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1vnegtzuppklyeqdr2ax.png)

xLayers 将分析该文件，并在您的浏览器中创建一个实例。这个实例叫做组件查看器，它用 HTML 表示设计好的草图文件。

[![xlayers-component-viewer](img/1f0ce608442223e722977ee5b824b5c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--haoqYfBZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jdhgyavz41pez80ykcp.png)

太好了，我们继续下一部分吧！

## 第二部分，协作

现在我们在组件查看器中，我们鼓励设计人员和开发人员坐在一起，就组件查看器中可见的设计部分进行对话。

xLayers 的目标和功能现在是可见的，您可以使用查看器就组件的不同方面进行对话，查看是否所有部分都是正确的。在查看器中，您可以通过使用左侧栏或单击层来轻松查看每个创建的页面及其层。通过选择一个层，该层将以红色指示边框高亮显示。

[![xlayer-layer-selection](img/c7ca7aa0710b8069ef2f032cfcad43be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dKrmJ59y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jf42g5611c5mtxolp6uz.png)

要查看更多细节，您可以使用顶部的工具栏，这些操作都有自己的目标。

[![xlayers-action-bar](img/f48d1edbee4658a75f6da8bae7205c52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--28II81Yy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/06m3b545fq9dj8k79fmz.png)

### 放大:

通过点击放大镜(如下所示)或按住 Ctrl +移动鼠标，可以使用放大操作。

[![xlayers-zoom-in](img/8a23e543a5a6fc99bb4e28635cb7ed34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hoeI0Rud--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g0ruresr887gl01d7ul0.png)

### 缩小:

可以通过单击放大镜(如下所示)或按住 Ctrl +移动鼠标来执行缩小操作。

[![xlayers-zoom-out](img/f969f9a2c6d5fbb4c9db77a0c58ea55f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DTV0mNCI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bpe3m33lmfwdoaoh2rwb.png)

### 重置为初始缩放:

通过按下“基本放大镜”,缩放级别将被重置为初始缩放。

### 3D:

当按下 3D 动作时，您可以通过 3D 查看方式查看所有不同的层。通过按下左移位和移动你的鼠标到一边，你甚至可以浏览所有不同的层，并从不同的角度看到它。

[![xlayers-action-3d](img/14acc253a65767192379bc2688a5ee60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ZQqMrVi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t38uwvth40cptja7nrb6.png)

## 第三部分，源代码:

工具栏中的最后一个操作是导航到代码生成的操作。导航后，您将拥有一组基于草图文件生成的代码。默认情况下，它会随着角度框架的选择而打开，但是，您可以根据自己的选择选择一个框架。

[![xlayers-code](img/f2dad199437d5aba6e68961ddf4f7319.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sRYd_qJV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v647jqw3bpkakgouo3v0.png) 
在这个页面上，你也能够使用工具栏。只是这次你的选择更少了。其中一个选择是使用 StackBlitz 作为您的在线 IDE。启用此操作后，所有代码都将发布到 Stackblitz，并且您将拥有一个可以在联机 IDE 中使用的项目设置🚀。

第二种选择是下载你所有的代码。使用此选项，您将获得一个可以下载到您机器上的压缩 zip 文件。这样，您就可以轻松地将它集成到所需的存储库中。

## 是什么带来了 xLayers 的未来？

目前，我们专注于以下主题:

*   不仅仅支持 web 框架，Xamarin 支持已经存在
*   拥有一个全新的草图解析器来处理位图和 SVG 以及更多可扩展性

## 谢谢

感谢您阅读我们的文章，我们希望这篇文章鼓励您尝试 xLayers，因为它相当容易使用，并在设计人员和开发人员之间的协作中提供了很多价值。

你愿意通过贡献来帮助我们吗？我们有第一次的问题，我们愿意以一切可能的方式帮助你。除了贡献之外，你还可以通过捐赠来帮助我们，这样我们就可以开发更多路线图上的功能。