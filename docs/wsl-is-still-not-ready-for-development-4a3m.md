# WSL 还没有为开发做好准备

> 原文：<https://dev.to/qodunpob/wsl-is-still-not-ready-for-development-4a3m>

大家好，我是一名前端开发人员，今天我想和大家说说我使用 WSL 的经历。

作为开发人员，我们选择 UNIX 系统是因为这是建立所需环境的最简单的方法。这就是为什么当你买了一台 windows 笔记本电脑后，你要做的第一件事就是安装 Linux。

但是自从微软推出了 WSL 之后，网上就有了很多关于如何利用这项技术建立一个便捷的工作环境的文章。这种方法的优点是您不会失去 windows 环境。有时，这对于您的硬件、用户体验或者如果您仍想使用任何 Windows 应用程序来说可能很重要。我真诚地尝试了大约一年。在此期间，我有幸参与了几个项目。然而，在几乎所有的学校里，我都遇到了各种各样的问题。

## 工具

首先，除了在 WSL 中安装 git 之外，我还必须在 Windows 上安装 git 来激活 [VSCode](https://code.visualstudio.com/) 函数，这让我有点恼火。这是 git 的两个不同版本，没有导致任何负面后果，但事实本身就令人担忧。我也试过这个解决方案【https://github.com/andy-5/wslgit[，但是](https://github.com/andy-5/wslgit) [GitLens 扩展](https://github.com/eamodio/vscode-gitlens)不能用。

第二件不愉快的事情是 Windows 控制台。必须要致敬，微软在最新版本中对其进行了大幅改进。然而，这仍然比不上 Linux 的解决方案。我知道有像 [Hyper](https://hyper.is/) 和 [Terminus](https://eugeny.github.io/terminus/) 这样的项目。但是它们比默认的 CMD 要慢。也许这是因为它们是基于电子的，我不确定。这就是我习惯 CMD 的原因。我还在 Windows 10 中使用了改进的窗口布局，在一个桌面上显示多达 4 个控制台窗口。对于在 windows 10 中使用 Windows 和桌面，我只能称赞微软。做得很酷。但你不是来听关于 Windows 和微软的赞美之词的，你想知道什么会让你心烦。

颜色；色彩；色调...CMD ~~中的颜色很差~~不好。我讨厌它的配置。有[伟大的交钥匙解决方案](https://gist.github.com/P4/4245793)通过 Windows 注册表改变 CMD 配色方案，我也创建了[我的配色方案](https://gist.github.com/bashkos/4a2e16aa259f16353c040896b42242f5) ( **注意**，有时文字颜色和选择的组合是不可读的)。但是要非常小心，因为如果您试图更改 CMD 属性设置中的配色方案，它将被保存在快捷方式数据中，并且不会应用注册表中的设置...[😫](https://twitter.com/bashkos/status/1100674793702010881)你可以在这里阅读更多关于这个[的内容。](https://devblogs.microsoft.com/commandline/understanding-windows-console-host-settings/)

下面我将谈谈我在工作中直接遇到的 WSL 的问题。

## 发展

如果您是一名前端开发人员，并开始您的移动应用程序开发之旅，也许您会选择 React Native，因为它非常接近您已经了解的内容。在这种情况下，Expo 将是一个很好的工具包选择，因为它非常方便，易于启动。我就这么做了...遇到了一个相当奇怪的问题，本期[https://github.com/expo/expo-cli/issues/99](https://github.com/expo/expo-cli/issues/99)对此进行了描述。

世博会工作几分钟后，所有网络请求都失败了。只有重启 WSL 有所帮助。在这种模式下工作非常困难。发现这个问题后，我找到了 Alexander Martin 的[评论:*可以确认 Windows Insider Build 18890 修复了这个问题*。由于建立工作流程对我来说非常重要，我不得不切换到内部预览模式。是的，在那之后，Expo 工作得很好，作为奖励，我得到了一个坏的休眠模式，从睡眠中醒来的问题，以及我的笔记本电脑上的声音的小问题。](https://github.com/expo/expo-cli/issues/99#issuecomment-490362830)

下一次我需要在本地安装 MongoDB，然而，正如[官网](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)上所说: *MongoDB 不支持 WSL* 。在这方面我没有解决方案，因为最后使用了远程连接。

说服我回到 Linux 的最后一个因素是需要在 electronic 上开发一个应用程序。因为 WSL 不支持窗口模式，所以这是不可能的。

除此之外，还有其他一些情况，比如某些东西不能编译或者不能像预期的那样工作，但是我记不起具体的细节了。

你可能会注意到，这些案例与前端开发没有直接关系，但我并没有给自己设定这样的目标。我只是想警告你，无论你做什么，WSL 可能会让你失望，有时这是很关键的。

## 结论

与 Windows 相比，我们更喜欢 Linux，因为它简化了工具的搜索、工作环境的设置，并允许我们专注于解决问题。然而，WSL 目前甚至比纯 Windows 更糟糕，因为它看起来像是完美的解决方案，却在最意想不到的时刻崩溃。同时，WSL 问题更难解决，因为对于社区来说，它仍然是一匹黑马。

现在我正在返回 Linux，我希望你考虑到我的经历，避免我的错误。不过，如果你还是决定自己尝试 WSL，我想[这个](https://github.com/sirredbeard/Awesome-WSL)会对你有用。感谢您的关注，请对您的案例发表评论。