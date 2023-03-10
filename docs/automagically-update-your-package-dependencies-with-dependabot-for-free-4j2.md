# 免费使用 Dependabot 自动更新您的软件包依赖关系！

> 原文：<https://dev.to/seankilleen/automagically-update-your-package-dependencies-with-dependabot-for-free-4j2>

随着[dependent bot 被 GitHub](https://dependabot.com/blog/hello-github/) 收购的消息传出，我认为这是一个很好的时机来解释我发现这个工具是多么的有帮助，以及你如何在你自己的库上设置它来毫不费力地保持你的包是最新的。

## 为什么保持依赖关系的更新很重要

*   **安全问题:**更新软件包通常是为了修复漏洞。如果你总是一点一点地更新你的包，这些问题就不会成为令人头疼的问题。
*   **避免大的彻底改变**:很多时候，项目在更新它们的依赖项之前会等待很长时间，这意味着当时间到来时，事情会由于库 API 或用法的差异而中断。

## 保持依赖关系更新的难点在于

但是更新软件包并不容易。许多团队推迟升级依赖关系是因为:

*   这可能会破坏事情:更新所有的依赖关系可能会有风险。我们如何知道升级后事情仍然会工作？
*   **很难知道哪个更新有问题**:当更新你所有的依赖关系时，如果出了问题，你必须花几个小时来理清头绪，找出是哪个依赖关系的变化出了问题。
*   这常常感觉像是一项巨大的任务:当依赖关系如此过时，你从哪里开始呢？你知道可能会有几个小时的痛苦。
*   **事情很快就变得过时了**:好像你刚刚协调完所有这些，又有新的更新了。恶心！
*   没有价值的无聊工作。

## 输入 Dependabot

Dependabot 是一个工具，可以让你的依赖项——NPM 包、nuget 包、ruby gems 等——以增量的方式保持最新。

我喜欢 Dependabot 的一些地方:

*   **它增量更新依赖关系**:dependent bot 不是一蹴而就，也不是将一个包升级到绝对最新的版本，而是以一种稳定、增量的方式带你去你需要去的地方，确保你不太可能遭受破坏。
*   **它为你做工作**:我*绝对喜欢*dependent bot 的一点是，它实际上会为你发出拉取请求，以及关于更新的信息和它是否预期会有突破性的变化。所以你甚至不用做大部分的工作。
*   它提供了方便的工具:另一个公关是否以某种方式引起了冲突？Dependabot 足够聪明，可以调整它的变化。
*   它将自己的配置与 repo 一起存储:你可以从 Dependabot 的界面设置它，但是你也可以将配置保存在一个 YAML 文件中，就在你的代码旁边。

因此，结合执行测试以确保事情正常运行的 CI 管道，Dependabot 是平衡编码早餐的一部分——它允许您以最少的麻烦保持更新。

## 为公共 GitHub 库设置依赖机器人

*   前往[http://dependabot.com 注册账户](http://dependabot.com)。
*   在[依赖机器人应用](https://app.dependabot.com)中，点击顶部菜单中的`Add Repos`:

[![Dependabot menu with a button for adding repos](img/48b0938ac3fa4154dad5908eafc57707.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z9KJydW_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://seankilleen.cimg/post-images/01_addrepo.png)

*   授予对存储库的访问权限
*   添加软件包使用的每种语言，并引用配置文件(例如，您的`gems`文件，或您的`package.json`文件，或您的`packages.config`文件)
*   设置存储库的首选项
*   …坐下来等待！Dependabot 现在将开始代表您发出请求。你可以配置频率，或者在你选择的任何时候开始“撞击”。

## 一个依赖机器人公关的例子

这里有一张最近的 PR，是 relative bot 为我博客的回购而开的。它包含了一些非常有用的信息:

*   更新的发行说明
*   作者作为更新的一部分所做的提交列表
*   基于 Dependabot 为其进行了此更新的所有其他项目的兼容性得分，以及这些项目是否有效。97%的兼容性？很好——更加自信！
*   我可以在 PR 中用 Dependabot 运行的命令列表来帮助我。

漂亮！我的博客有一个通过 Netlify 的 CI/CD 流程——如果 Netlify 可以运行并生成预览，这告诉我我们可能做得不错。所以最后，我看到了一个通过了笔记和测试的 PR，以及我的博客的预览。这让我对变化感觉很好，我可以很快合并结果。

## 稍加努力，Dependabot 也可以和私有库一起使用！

好吧，这很好——但是如果我的存储库被托管在 GitHub Enterprise 上，在防火墙后面，而 Dependabot 永远无法访问它，那该怎么办？

…好吧，关于 dependent bot 的人们已经很棒了，足以[制作 dependent bot-core OS](https://github.com/dependabot/dependabot-core)，因为正如他们的自述文件所说:

> 我们更感兴趣的是它的影响力，而不是从中赚钱。

这难道不是一件好事吗？如果人们感兴趣，我可能会在不久的将来做一个教程。

所以，向前迈进，让事情保持最新。编码快乐！