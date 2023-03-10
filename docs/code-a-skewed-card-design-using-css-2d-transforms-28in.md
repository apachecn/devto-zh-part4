# 使用 CSS 2D 变换编码倾斜卡设计

> 原文：<https://dev.to/hunterbecton/code-a-skewed-card-design-using-css-2d-transforms-28in>

## 简介

[CSS 2D 变换](https://www.w3schools.com/css/css3_2dtransforms.asp)允许你修改元素的形状、大小和位置。目前有 9 种方法可用于`transform`属性:

*   `translate()`
*   `rotate()`
*   `scaleX()`
*   `scaleY()`
*   `scale()`
*   `skewX()`
*   `skewY()`
*   `skew()`
*   `matrix()`

今天我们将使用`skewX()`、`skewY()`、`scale()`和`rotate()`来创建以下卡片设计:

[https://codesandbox.io/embed/xt4qe](https://codesandbox.io/embed/xt4qe)

**注意:你也可以用`skew(x,y)`作为`skewX()`和`skewY()`的简写。**

下面是一个循序渐进的视频，如果你愿意的话，可以看看。否则，每个步骤都会在视频下方列出。让我们开始吧！

[https://www.youtube.com/embed/GHpq3gJYtmU](https://www.youtube.com/embed/GHpq3gJYtmU)

## 开始使用

### 开发环境

我们将使用 [VS Code](https://code.visualstudio.com/) 作为代码编辑器，安装了 [HTML5 样板文件](https://marketplace.visualstudio.com/items?itemName=sidthesloth.html5-boilerplate)和 [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) 扩展。如果您没有安装，也可以按照这里的快速文档使用 Mac 从命令行启动 VS 代码项目。

### 创建项目

让我们使用 VS 代码命令行来创建一个项目。打开 VS 代码，选择*端子>新建端子*。在 VS 代码的底部会打开一个终端窗口。如果你是新手或者需要复习终端命令，看看这个 [Mac 终端备忘单](https://gist.github.com/poopsplat/7195274)。

使用`cd`(更改目录)命令，后跟目录的位置，找到您想要创建项目的目录。您可以先在命令行中键入`ls` (list)来了解一下有哪些功能。我将通过键入`cd Desktop/`并按回车键进入桌面。如果你需要返回一个目录，你可以在终端输入`cd ..`。

注意:您可以开始键入，然后按 Tab 键自动完成。

我们将通过使用`mkdir` (make directory)命令后跟我们的项目名称来开始创建项目。我们来做`mkdir slanted-card`。一旦目录建立，我们需要输入`cd slanted-card/`并按回车键进入目录。

在倾斜的卡片目录中，我们将通过键入`mkdir images`并按回车键为`images`创建另一个目录。然后我们将使用`touch`命令:`touch index.html styles.css`创建两个文件。现在在命令行中键入`ls`来列出倾斜卡目录中的所有内容。您应该会看到`images index.html styles.css`被打印出来。

接下来，键入`code .`在新的 VS 代码窗口中打开项目。

**注意:你需要在 Mac 上使用[从 VS 代码文档中启动项目](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line)来设置这个`code`快捷方式。**

在开始编写 HTML 之前，我们需要做的最后一件事是将徽标导入到`images`文件夹中。你可以[下载我在这里使用的标志](https://www.dropbox.com/sh/ne6w7rpelkdbdoy/AAAKpXLM0DsLBq1IeORE_83-a?dl=0)(提供了 3 种以上的变化)，但可以随意使用你自己的。我推荐使用 20-25 像素高的 SVG。

## 编写 HTML

在 VS 代码中打开`index.html`文件，开始输入`html`，`html-boilerplate`应该会出现在下拉菜单中。选择此项并按 return，HTML5 样板文件扩展将插入样板文件代码来启动您的项目。随意填写标题和描述元数据，但这对于设计来说不是必需的。然而，在`<link rel="stylesheet" href="styles.css">`标签中导入`styles.css`文件是很重要的。

去掉从样板文件扩展中插入的开始和结束标签之间的所有内容。在正文标签内写下以下内容: