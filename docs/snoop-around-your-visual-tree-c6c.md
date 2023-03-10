# 窥探你的视觉树

> 原文：<https://dev.to/dejavo/snoop-around-your-visual-tree-c6c>

[![Visual Tree — Photo by [Johann Siemens](https://unsplash.com/@johannsiemens?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)](img/0c4ceee6fb359d780a00275a4f7c29d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6QCGGqKu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AZfXTwwHsgllR6nzJ)

 *原载于[中](https://medium.com/@dvirsegal/snoop-around-your-visual-tree-81c6b4e311b3)月<time title="Thursday, June 20, 2019, 10:41:16 AM">日 2019 年</time>* 

绑定问题？与难以解决的 UI bug 做斗争？试图理解如何将整个字符串放入 TextBox 的维度？这些情况和许多其他情况对我们来说很常见。NET 和 WPF 开发者。

如上所述，大多数情况下，问题是通过使用调试器来解决的。

想象一下；您想要更改特定图形元素的大小。好，你打开相关的项目(使用 [Everything](https://medium.com/@dvirsegal/its-all-about-everything-95043d1b0aa) tool😉)在您最喜欢的 IDE 中，进行一些更改，编译、部署并重新运行应用程序。然后你附加一个调试器。调查并重新运行整个过程，直到解决问题。

嗯，这需要时间。很多。

[Snoop](https://github.com/snoopwpf/snoopwpf) 由 [Pete Blois](https://github.com/peteblois) 开发，以减轻调试你的 WPF 应用程序的痛苦。这个开源的 WPF 间谍工具，现在由[科里·普洛茨](http://www.cplotts.com/)维护，允许动态改变可视化树，排除绑定故障，等等…

[![[xkcd: Compiling](https://imgs.xkcd.com/comics/compiling.png)](img/ae7c0463554d689e7ccab864d1ce01f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PZYgVO2t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AJ-1MC3QGbIuwq4tb-yr-iA.png)

#### 偷窥你的 app

从 Github 资源库下载并安装 Snoop:

[**【snoop wpf/snoop wpf**](https://github.com/snoopwpf/snoopwpf/releases)

启动 Snoop 并将 Snoop 十字线拖到您的 WPF 应用程序中。

[![Snoop window, the snooping cross-hair is marked with a red rectangle](img/1a4e50bfc8c011ef8bcac23f89208374.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h5vgyTVd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A83wftet6QNifOIUPTey1Eg.png) 
探听窗口中，探听十字线被标上一个红色的长方形

将打开一个新窗口。在左侧，显示应用程序的可视化树，而在右侧，列表中的每个元素都可以看到属性编辑器。下面的 GIF 演示了 Snoop 的主要用法。可以看到 TextBlock 的一个文本属性内容正在被动态编辑。

[![Snooping example](img/514197ff161f485a43a2f3c9e0e7727a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FKeNi_vQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AnM5_Hji8Gx5dztEjUdzoHQ.gif)

#### 特性

按住 Ctrl+Shift 键并悬停在所需的元素上可以提高工作效率。它会使 Snoop 立即跳转到被悬停元素的属性，这样可以省去您在可视化树中查找所需控件的工作，如下所示:

[![Ctrl+Shift trick](img/4aab7b5d687058fdfe9a40bfd24c4f4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bffCk7YM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AtXSaNQgXt0Sf8i77KOea9A.gif)

另一个技巧是在过滤器框中搜索您想要更改的属性。此外，一个有用的功能是“只显示有绑定错误的视觉效果”选项，位于视觉树视图上方的搜索栏下拉列表中。

[![Show only visuals with binding errors](img/2af1cd02ce4b0f1a2c653f29c20740c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pmv47iu9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2At_1gdBtJFlQmYl_6dTwv7w.png)

它显示了有绑定问题的组件。通过这种方式，可以节省使用 IDE 识别任何绑定失败的时间。

[![[Moar Features](https://memegenerator.net/instance/65792011/roar-cat-moar-features)](img/f48a2a6bffd25dd7b54c3f9ffdfb042e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DzB6qY39--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2A2t29S2ejFM1RrcLB.jpg)

Snoop 中有更多较少使用的功能(IMHO)。例如，power-shell 是一个控制台，您可以在其中编写方法并通过命令与 UI 进行交互。“事件”选项卡，根据您的有限选择列出所有触发的事件,“触发器”选项卡，用于通过触发器调试控件或样式发生的任何更改，无论它是属性触发器、数据触发器还是事件触发器。

#### 总结

虽然它的用户界面不太友好，但我发现它在查找缺失的绑定和调整布局参数方面非常方便。此外，克服了调试不熟悉的复杂用户界面的努力，因为 Snoop 会投影视觉树，并允许在其中快速导航。

此外，类似的 WPF 间谍实用程序也存在(如[鼹鼠](https://www.codeproject.com/articles/21441/mole-for-wpf-multifunction-visual-studio-visualize)和 [WPF 检查员](http://www.wpftutorial.net/Inspector.html))，但 Snoop 有一个活跃的开源开发，它可以省去您使用 IDE 调试应用程序的麻烦。我无法想象没有它开发 WPF 界面。

顺便说一下， [HawkEye](https://github.com/odalet/Hawkeye2) (由 [Olivier Dalet](https://github.com/odalet) 维护)是另一个具有类似功能的开源工具，允许窥探你的 WinForms 应用程序。