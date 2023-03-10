# 为 VS 代码定制你自己的主题

> 原文：<https://dev.to/js2me/make-your-own-custom-theme-for-vs-code-me7>

大家好！:)

几天前，我发现了一些扩展，允许你加载自定义的 CSS 和 JS 文件到 Visual Studio 代码中。如果您有兴趣修改您的使用主题或为 Visual Studio 代码创建自己的 CSS 样式，本文可以帮助您:)

我们需要的扩展是`Custom CSS and JS Loader`。你需要安装它

[![Custom CSS and JS Loader](img/413552d5754e082a058759cc806beaab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7luVsTSp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Atd1yiY.png)

安装后，您需要打开此扩展。用`Ctrl + Shift + P`或`Shift + ⌘ + P`或`F1`打开你的命令面板，选择**“启用自定义 CSS 和 JS”**。它会提示您重新启动。

下一步，您需要在您的文件系统上创建 CSS 文件，并将该文件的路径添加到 VS 代码的`settings.json`中的属性`vscode_custom_css.imports`。

```
{  "vscode_custom_css.imports":  [  "file:///{PATH_TO_THIS_FILE}/{YOUR_FILE_NAME}.css"  ]  } 
```

如果你正在使用 Windows(像我一样)，你需要像这样写文件的路径

```
{  "vscode_custom_css.imports":  [  "file:///C:/Users/User/vs_dark.css"  ]  } 
```

您可以将以下样式之一放入此 CSS 文件:

*   [霓虹 CSS 样式(synthwave84.css)](https://github.com/robb0wen/synthwave-vscode/blob/master/synthwave84.css)
*   [我的 VS 代码黑暗主题扩展](https://gist.github.com/js2me/dfccef039e1ce727eafce4145c0bb4cb)

此外，如果你附加了`synthwave84.css`你应该[安装这个主题到 VS 代码](https://marketplace.visualstudio.com/items?itemName=RobbOwen.synthwave-vscode)或者如果你使用我的 VS 代码黑暗主题扩展你应该在 VS 代码中选择`Dark+ (default dark)`主题

在此之后，您需要重新加载 VS 代码来启用这个自定义的 css 文件。为此，你需要打开命令面板，选择**“重新加载自定义 CSS 和 JS”**

之后，您应该会在 VS 代码中看到 CSS 修改:)

[![](img/ea7c0d1e05271b67007a117168f98865.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LsH4Bn-g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/cB7riwB.png)

或者，如果你添加了`synthwave.css`，你的 VS 代码编辑器应该是这样的

[![](img/8522ccbf1f9b0242660654b2694c6209.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h_QobY_F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.redd.it/jfr0ixsgm9v21.jpg)

如果你想调试和修改这个文件，你需要打开命令并选择**“开发者:切换开发者工具”**。

[![](img/da23c69e198ea610838762f637a9c9be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sQ9i5I1o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/EB0GlmR.png)

它开放了与谷歌 Chrome 相同的开发者工具。

[![](img/b39caaace391ea88f6cf06ee285f5f25.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DHhFsyFQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/dmI9Uj5.png)

并且您的自定义样式正在覆盖现有的

[![](img/ae1f88fcd93e866fa033a0e20541db48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qWGgt3TM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/0yM1RTf.png)

**如果你有什么问题不要担心，在这里问**
**感谢阅读这篇文章，享受:)**

### 额外链接

*   [定制 VS 代码的 CSS 和 JS 加载器](https://marketplace.visualstudio.com/items?itemName=be5invis.vscode-custom-css)
*   [为 VS 代码定制 css 扩展](https://marketplace.visualstudio.com/items?itemName=RobbOwen.synthwave-vscode)

[![](img/72214424c0ce50bb1313546b32128b07.png)](https://i.giphy.com/media/Ydvn0G7Agtk9G/giphy-downsized.gif)