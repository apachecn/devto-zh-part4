# 在线 IDE 中您最喜欢的 VS 代码扩展

> 原文：<https://dev.to/svenefftinge/your-favorite-vs-code-extension-in-an-online-ide-3en5>

今天，我们很兴奋地宣布下一代云 IDE[git pod](https://www.gitpod.io)中 VS 代码扩展的公开测试阶段开始。

到目前为止，Gitpod 支持主流语言，如 JavaScript、Java、Python、Ruby、Rust、C++和 Go，但许多其他语言尚未涉及。在今天的版本中，我们增加了对安装任何 10K+现有 VS 代码扩展的支持。最后，Scala、Swift 和 F#开发人员在 Gitpod 中也会感觉如鱼得水。

## 项目配置的一部分

Gitpod 会根据需要自动创建全新的、一次性的开发环境。不像传统的开发环境，您只需设置一次，然后就必须维护一段时间，您可以为每个任务获得一个全新的环境。因此，当丢弃工作区时，任何已安装的扩展通常都会消失。

这是因为 Gitpod 开发环境是为手头的项目定制和优化的。我可能会参与多个不同的项目，例如一个 Scala 项目以及一些 Node.js 项目。为什么我需要在我所有的项目中都有 Scala 扩展？

> 传统上，您会为所有项目维护一个臃肿的编辑器，或者为每个项目维护多个编辑器。

Gitpod 为您解决了这个问题，它提供了一个小配置，它是您的 git 历史的一部分。这包括关于依赖项、启动任务的信息，现在还列出了项目在那个时间点(版本)所需的扩展。

它是这样工作的:

## 安装分机

安装扩展就是拖放一个*。vsix 文件到扩展视图中。Gitpod 将自动安装并激活新的扩展，并在. gitpod.yml 文件中添加一个条目。您现在可以测试这种体验，如果您满意，可以通过将更改推送到您的远程 Git 存储库来与您的团队分享这种新体验。这里有一个小截屏:

[https://www.youtube.com/embed/hEqmXM62cjo](https://www.youtube.com/embed/hEqmXM62cjo)

## 在哪里可以找到扩展？

微软禁止任何其他软件直接使用 [Visual Studio Marketplace](https://marketplace.visualstudio.com/vscode) ，除了他们自己的软件。尽管大多数扩展都是开源的，甚至不是由 MS 开发或维护的，但是进入市场是受限制的。
然而，如果你有可用的`.vsix`文件，你可以自己上传扩展。`.vsix`文件不仅可以从 Visual Studio Marketplace 获得，也可以在 GitHub 上找到，例如[beauty-vs code](https://github.com/prettier/prettier-vscode/releases)。

一个扩展只需要上传一次，之后你和你的队友都可以使用。请确保您遵守这些扩展的许可。
此外，当然，你可以开发和上传自己的扩展。除了在你自己的`.gitpod.yml`文件中，它们不会在任何地方公开列出。

## 你的扩展不按预期工作？

覆盖 VS 代码扩展的全部 API 是一项艰巨的任务，忒伊亚社区已经为此努力了一年多。

当我们接近实现的 100%覆盖率时，是时候向更多用户公开这个特性了。这就是为什么我们决定将它作为 beta 版提供给 Gitpod 社区。

因此，如果你想帮助我们强化这个令人兴奋的新功能，请尝试你最喜欢的扩展，并提交你认为需要改进的忒伊亚项目问题。

编码快乐！