# Galois:基于 OpenAI GPT-2 的代码编辑器的自动完成器。

> 原文：<https://dev.to/iedmrc/galois-an-auto-completer-for-code-editors-based-on-openai-gpt-2-40oh>

今天我介绍  (/ɡælˈwɑː/)这是一款基于 OpenAI GPT-2 的开源自动代码补全器。

Galois 在从 Github 收集的大约 45K Python (~470MB)文件的精选列表上被训练(微调)。目前，它只能在 Python 上正常工作，但在其他语言上还不错(多亏了 GPT-2 的强大)。

[![Galois demo GIF](img/d6daf6188fbef310344746c2d46071ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p8GCXa6J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/iedmrc/galois-autocompleter/master/img/python1.gif)

通过这个项目，我的目标是创建一个基于**深度学习的自动完成器**，这样任何人都可以在自己的电脑上轻松运行它。我最初的灵感来自于 TabNine，但我想让**伽罗瓦**可以在最终用户的电脑上自由运行(就像在《自由》中一样)。因此，任何人(尤其是开发人员)都可以免费从最新的 NLP 技术中受益，而无需与服务器共享代码(或文本)。为了实现这个目的，我们需要像 [EfficientNet](https://paperswithcode.com/paper/efficientnet-rethinking-model-scaling-for) 一样，在许多数据集中获得最先进的结果。目前，**伽罗瓦**在 GPT-2 345M 上训练，没有开发出如此高效的深度学习模型。查看[计划作品](https://github.com/iedmrc/galois-autocompleter#planned-works)此处为**伽罗瓦**的未来。

你可以在[usegalois.com](https://usegalois.com/)上试试。它得到了麻省理工学院的许可，所以你可以在[github.com/iedmrc/galois-autocompleter](https://github.com/iedmrc/galois-autocompleter)上找到**伽罗瓦**的训练模型、代码和一些指令，在[github.com/iedmrc/galois-web](https://github.com/iedmrc/galois-web)上找到在线编辑器。检查`docker-compose`文件在处的链接[。](https://gist.github.com/iedmrc/1e41197a6a2f7a9a654a0df9bd932290)

欢迎任何投稿。伽罗瓦背后的技术已经由社区开发了很多年。所以很明显我们可以说它属于社区。

为了使人工智能民主化，让我们向全世界传播这个词！拥有它，使用它，贡献它，分享它！

关于[évariste 伽罗瓦](https://en.wikipedia.org/wiki/%C3%89variste_Galois)(【evaʁist ɡalwa]).