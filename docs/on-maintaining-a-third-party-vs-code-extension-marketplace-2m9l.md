# 维护第三方 VS 代码扩展市场

> 原文：<https://dev.to/foresthoffman/on-maintaining-a-third-party-vs-code-extension-marketplace-2m9l>

在我在 Coder.com 做的各种事情中，最有趣的一个项目是内部推广市场。这个市场与[代码服务器](https://github.com/foresthoffman/code-server)客户端接口，允许用户在他们的服务器驱动的 VS 代码实例上安装扩展。由于官方的 [VS 代码市场](https://marketplace.visualstudio.com/vscode)在技术上或者法律上[不可用于第三方(现在仍然不可)，我们不得不推出我们自己的！](https://code.visualstudio.com/license)

我们的内部市场利用 Google 云存储平台为代码服务器用户支持超过 4500 个扩展。

这太棒了，但是这个项目尤其具有挑战性，原因有三:

1.  官方市场不能直接使用
2.  官方市场看起来没有任何重大的策划
3.  一些扩展没有...易于支撑...像其他人一样

[![Coding with flames gif](img/40af5680a4f0087f9515c035f2423848.png)](https://i.giphy.com/media/13HgwGsXF0aiGY/giphy.gif)

###### 边注:当我写这个的时候，让我们看看我能说多少次“官方的”或“正式的”。

### 1。🚧官方禁止入内🚧

由于官方市场是禁区，内部系统必须提供有效的扩展，包括所有的元数据。比如:

*   显示
    *   作者、存储库、版本等。
*   核标准情报中心
*   描述
*   许可证
*   扩展档案
*   杂项文件
    *   变更日志

显然，这需要手动处理大量数据，因此我们有一个辅助服务来收集所有数据。这就是延伸刮板的由来。这项服务负责提取开源扩展库并打包分发。

### 2。🕵️官方神秘的🕵️

可靠性是这个游戏的名字，所以铲运机的工作不仅仅是把源拉下来，打包，然后把它们倒进存储桶。它需要确保扩展是:根据官方标准有效；发挥了作用。

官方标准在技术上可以通过微软的 [vsce](https://www.npmjs.com/package/vsce) CLI 工具进行检查。这个工具实际上是输出`*.VSIX` Zip 文件的工具。如果它遇到任何异常，它不会打包扩展。

在数千个扩展上手动和编程地运行了这个命令之后，我可以有把握地说，我在没有成功的扩展中看到的最常见的错误是，当扩展包含作为依赖项的 [vscode](https://www.npmjs.com/package/vscode) 模块时。vscode 模块是由 VS Code 运行时环境提供的，所以它只需要作为一个开发依赖项。这发生在目前官方市场上的扩展中。

因此，考虑到其他错误，尤其是粗略的存储库 URL 或不完整的元数据，我唯一的结论是市场没有任何监管。然而，这只是不太流行的扩展的问题。这在微软官方支持和维护的扩展中并不明显。想想 [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 或者 [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell) 。或者，甚至有强大社区支持的扩展，如 [Vim](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim) 。这些都是很棒的工作，任何问题都很容易报告和处理。

[![Patrick daww gif](img/9884426e86659e6e16df2cd373ac9485.png)](https://i.giphy.com/media/UVk5yzljef0kGiayL1/giphy.gif)

有道理。大约一百万人使用的所有流行的扩展都需要稳定并符合标准。令人担忧的是规模较小但仍然重要的团体使用的扩展。

除了打包之外，scraper 还需要确保扩展能够为用户所用。为了检查这一点，scraper 将每个扩展源加载到 Docker 容器中，并尝试加载所有扩展的依赖项。这需要包括本地和全球依赖性。同样，对于绝大多数人来说，这非常有效。然而，一些异常值令人担忧。

有一个扩展仍然在引用并试图下载一个在过去经历过严重安全问题的旧 NPM 模块。我找到了扩展维护者，并解决了这个问题。不过，这让我想知道市场上被忽视的扩展还有哪些潜在的安全风险。

如果有一些工具或自动提交管道来扩展就好了。理想情况下，这样的管道将确保提交的扩展符合我前面提到的非常流行的扩展的标准。

### 3。🔥注意:易燃🔥

为广泛使用的开源项目的第三方分支创建第三方填充的一个不幸的缺点是，您必须逆来顺受。你受官方维护者的支配，也受你想在你的平台上支持的所有扩展维护者的支配。

因此，在某些情况下，如[设置同步](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)(旁注:我个人很喜欢它😍)由于 VS 代码本身缺乏自定义功能，扩展维护人员受阻。这意味着我们的用户要么祈祷功能被交付，要么采取一些复杂的步骤来支持它！两者都不理想。

有一些存储库，比如[本地化扩展包](https://github.com/Microsoft/vscode-loc)，包含了非常激进的版本约定的扩展。每次 [VS 代码知情者](https://code.visualstudio.com/insiders/)更新的时候，有问题的扩展就会把他们的版本号更新到一个我们明显不支持的 VS 代码版本！

[!["Oh come on" meme](img/24de35d5c06a906457e1621b46ef8a59.png)](https://i.giphy.com/media/MwOuiiTfWfWgM/giphy.gif)

这导致我为扩展刮刀创建版本约束。这样，积极版本化的扩展，或由于不兼容而需要回滚的扩展(例如，[https://github.com/VSCodeVim/Vim/issues/3522](https://github.com/VSCodeVim/Vim/issues/3522))可以继续为我们的用户发挥预期的功能。

* * *

总的来说，工作很有趣！感谢阅读。

[![Thumbs up](img/b6ddf74892a198054e6d3c080cec4c87.png)](https://i.giphy.com/media/GCvktC0KFy9l6/giphy.gif)

###### 看起来一共有 9 个“官方”的实例和 4 个“官方”的实例！🤣

* * *

图片来源:[伯纳德·赫尔曼](https://unsplash.com/@bernardhermant?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/market?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片