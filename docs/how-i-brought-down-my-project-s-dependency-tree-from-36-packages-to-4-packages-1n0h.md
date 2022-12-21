# 我如何将项目的依赖树从 36 个包减少到 4 个包

> 原文：<https://dev.to/saurabhdaware/how-i-brought-down-my-project-s-dependency-tree-from-36-packages-to-4-packages-1n0h>

在你`npm install package`或`<script src="https://coolframework.com/file.js">`之前，你有没有问过自己是否真的需要这个包/框架/库？

有什么方法可以让我用自己的函数实现同样的东西吗？

如果这个包有 300 个函数，而我需要 2 个函数，那么它真的值得放在我的依赖项中吗？

当我开始我的项目 [`ProjectMan`](https://github.com/saurabhdaware/projectman/) 并安装了 3 个包 [Commander.js](https://github.com/tj/commander.js/) 、 [Inquirer.js](https://github.com/SBoudrias/Inquirer.js/) 和 [Chalk](https://github.com/chalk/chalk) 时，我并没有问自己这些问题。

但是那最终在我的依赖树中放了 36 个包！所以`npm install -g projectman`正在安装 37 个软件包。如果这些包中的任何一个坏了怎么办？我真的需要让人们安装 36 个包来运行我简单的命令行工具吗？更多的包=更多的时间到`npm install`，如果有人取消安装怎么办？

所以在 v1.2.0 之后，我决定为 ProjectMan 的 v1.3.0 尽可能地最小化这个大小，并开始一个一个地替换我的依赖项。

## 目标 1::粉笔

这是一个为控制台上的文本着色的库。

它有 6 个依赖项(包括直接和间接依赖项)

但是我真的不希望彩虹流过我的用户的控制台🌈我只是想要一些颜色..所以我简单地检查了一下`chalk.bold.red("HelloWorld")`返回的内容，它返回了这个可怕的字符串:

```
`\u001b[1m\u001b[31mHelloWorld\u001b[39m\u001b[22m` 
```

正如你所看到的，在这个字符串中有 HelloWorld，我试着用其他文本替换它，它仍然工作。我对我使用的所有颜色做了同样的事情，并简单地在我的项目中创建了一个`colors.js`文件，现在看起来有点像

```
// Just some weird strings that color text inside it. You probably will not have to touch this file.

exports.green = (message) => `\u001b[32m${message}\u001b[39m`;    

exports.boldGreen = (message) => `\u001b[1m\u001b[32m${message}\u001b[39m\u001b[22m`;

exports.boldRed = (message) => `\u001b[1m\u001b[31m${message}\u001b[39m\u001b[22m`;

exports.yellow = (message) => `\u001b[33m${message}\u001b[39m`;

exports.boldYellow = (message) => `\u001b[1m\u001b[33m${message}\u001b[39m\u001b[22m`;

exports.grey = (message) => `\u001b[90m${message}\u001b[39m`;

exports.boldGrey = (message) => `\u001b[1m\u001b[90m${message}\u001b[39m\u001b[22m`;

exports.bold = (message) => `\u001b[1m${message}\u001b[22m`; 
```

这 17 行(包括换行符和注释)替换了 7 个包！！！

然后**嘣**我的包减少到 30 个依赖项。

以下是我在 repository 中为此所做的更改:
[https://github . com/saurabhdawel/project man/commit/413355 b41d 87 ff 18 c 9 DCF 02 bebf 51 D3 da 35372 B3](https://github.com/saurabhdaware/projectman/commit/413355b41d87ff18c9dcf02bebf51d3da35372b3)

## 目标 2 :: Inquirer.js

Inquirer 提供了漂亮的界面，以列表、文本和许多其他选项的形式接受输入。

我个人很喜欢这个库，但是唯一困扰我的是它的依赖性。Inquirer 依赖于 28 个包(包括直接和间接依赖)。即使对于 inquirer 提供的功能，28 个包也太多了！

我没有办法用我自己的函数来实现它，因为它有太多的特性，我不可能把所有这些特性都编码出来。

于是我开始四处寻找替代品，发现[提示](https://www.npmjs.com/package/prompts)。

Prompts 可以做 inquirer 可以做的几乎所有事情，并且有 3 个包的依赖(包括直接和间接)！！虽然我觉得提示的一些功能不如 inquirer 稳定，但对我来说，经过一些小的变通后，它工作了。

而且 **Boom** 4 个包替换了 29 个包！放映员只剩 5 包了！！！

## Commander.js

Commander 是一个非常棒的库，并且有 0 个依赖项，所以我仍然在使用它，我非常喜欢它！

# 结论

我的包在 4 个依赖项上的运行与在 36 个依赖项上的运行完全相同，并且没有给我带来任何可伸缩性问题或任何主要特性的故障。

在你安装一个脚本/包/框架之前，先等一下，问问你自己这三个问题

*   我真的需要这个图书馆吗？
*   我需要什么功能？有没有什么方法可以让我编写自己的函数，而不用花费很多时间，也不用考虑可伸缩性或其他问题？
*   如果我不能写自己的函数，那么有没有其他稳定的替代包，使用较少的依赖性，并且不会破坏你的包？

# 也，

我并不反对这些库中的任何一个，在某些特殊的情况下，你可能真的需要这些库中的很多功能，在这种情况下你应该完全使用它们。

感谢您的阅读！请发表评论，让我知道你对这个:D 的想法

Twitter:[@ saurabhcodes](https://twitter.com/saurabhcodes)
Github:[@ saurabhdawse](https://github.com/saurabhdaware)
project man Repo:[/saurabhdawse/project man](https://github.com/saurabhdaware/projectman)