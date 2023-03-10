# 介绍在电子版中使用 p5.js 的快速入门模板

> 原文：<https://dev.to/camerenisonfire/introducing-a-quick-start-template-for-using-p5-js-in-electron-8k5>

看到自己编写的代码变得栩栩如生，是一种独特的享受。这就是为什么我最喜欢做的许多项目都是创造性的编码项目。其中大多数都围绕着 [p5.js](https://p5js.org) ，基于[处理](https://processing.org)语言的 Javascript 库。

> 这是我的博客 [Imago Dev](https://imago.dev) 的交叉帖子。

Processing Foundation 的目标是让艺术家和设计师能够使用编码。这个库通过提供一个易于理解的 API 来在 web 浏览器中绘制和制作动画。从通过移动原始形状学习编程，到创建复杂的生成艺术，p5.js 都支持您。

### 道路上的坎坷

尽管 p5.js 很简洁，但 web 浏览器本身也有一些限制。你必须启动本地服务器来检索资产，你无法访问文件系统，你被限制在一个标签和窗口中，如果你像我一样，你很可能会被打开的 Youtube 标签分散注意力。

这就是电子进入的地方。通过结合 Chromium 和 Node.js，你可以用一个工具解决所有这些问题(可能不是 Youtube 的干扰)。

电子和 p5.js 配对很棒。Electron 会满足您所有的文件系统、窗口和菜单栏需求，让 p5.js 做它最擅长的事情——创建超级酷的艺术和设计。草图从一个小小的网页变成了一个成熟的本地应用。

为了帮助您在 electronic 中创建 p5.js 草图，我构建了一个快速启动模板—[P5—electronic—快速启动项目模板](https://github.com/camerenisonfire/p5-electron-quick-start)。

### 特性

该模板预配置有:

*   `p5.js` npm 模块，而不是静态下载的文件。
*   `Electron`带有预配置的主和渲染过程文件。
*   `Electron-reload`在您进行更改时保持草图最新。
*   `Mocha`作为 Node.js 测试套件。
*   对鼠标移动和单击做出反应的示例草图。
*   创建纯函数来驱动草图的示例。
*   为你的素描做测试。

对于那些熟悉 p5.js 草图的人来说，你会注意到`sketch.js`文件的设置有点不同。为了将 P5 排除在全局范围之外，它在`sketch.js`内部被实例化，而不是在`index.html`文件中声明为`<script>`。这意味着无论何时调用 p5 方法，都必须从注入的 p5 实例中调用它。例如，在`sketch()`函数中，你必须使用`p.ellipse()`而不仅仅是`ellipse`。

### 只需按下一个按钮就可以离开

Github 通过`Use this template`按钮使从模板项目创建新的回购变得简单。在那之后，再加上几个`npm`命令，你就可以跳过烦人的设置，直接在 electronic 中用 p5.js 构建令人敬畏的创造性代码了！

希望这个模板能在你的下一个项目中很好地为你服务。请提供任何反馈，说明如何改进这个快速入门工具来帮助您。