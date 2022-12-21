# 网络组装并不总是答案

> 原文：<https://dev.to/jtenner/web-assembly-isn-t-always-the-answer-3nd5>

当我听到“JavaScript 黑仔”这个词时，我会联想到“杀死 JavaScript”的欲望事实上，我们所有人都想避免在我们的网站或网络服务器上增加交互性所带来的问题。原因是因为 JavaScript 速度和性能是一个“我必须做出什么牺牲才能鼓励这个软件良好运行”的游戏没人喜欢这个。

只是大多数关于这个问题的评论都会源于一堆负面的东西，我认为这完全是完全错位的。我希望通过指出 Web 组装并不总是*的*解决方案来解决一些关于速度的问题，希望 JavaScript 的怨恨能够消散。

让我们从 Web 开发的一些基础知识开始，包括执行速度、内存使用、垃圾收集时间、帧速率和开发的难易程度。将 Web Assembly 作为一个工具可以(而且*也确实*解决了许多速度和内存使用方面的问题。

前面提到的每一个问题都可以用 JavaScript 有意义地处理，除了开发的容易性，因为这需要个人的责任感和聪明的态度。然而，对于速度和垃圾收集，我现在选择`AssemblyScript`作为我的首选工具。

AssemblyScript 本身解决了很多我想解决的问题。它有管理内存、垃圾收集，而且作为一个权衡，它“足够简单”,因为我能够用一种特殊风格的 JavaScript 编写我的软件。这恰好是我个人关注的焦点，我非常喜欢它，以至于我为它开发了一个完整的测试框架。

关于 AssemblyScript 有很多要说的，我在这里写了一篇关于它的入门文章。我想用它作为一个例子，说明为什么网络组装并不总是解决你所有问题的答案*。你可以在这里查看 GitHub(并给它一颗星):*

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [组装脚本](https://github.com/AssemblyScript) / [组装脚本](https://github.com/AssemblyScript/assemblyscript)

### 肯定不是 WebAssembly 编译器的类型脚本🚀

<article class="markdown-body entry-content container-lg" itemprop="text">

[![AssemblyScript logo](img/991b4653baee4f5000ba823d7265b53e.png)](https://assemblyscript.org)

[![Test status](img/76340ce00a0a10c745566947f91c0a1c.png)](https://github.com/AssemblyScript/assemblyscript/actions?query=workflow%3ATest)[![Publish status](img/699ea13a9ee0376aca5fde6fce52fcaf.png)](https://github.com/AssemblyScript/assemblyscript/actions?query=workflow%3APublish)[![npm compiler version](img/8145eb2c775dde2e06500dca701c0ad9.png)](https://www.npmjs.com/package/assemblyscript)[![npm loader version](img/9cea788c57f063697d742cfe1721459d.png)](https://www.npmjs.com/package/@assemblyscript/loader)[![Discord online](img/f55d42249a841f29fffb440efb3aa83e.png)](https://discord.gg/assemblyscript)

**AssemblyScript** 使用 [Binaryen](https://github.com/WebAssembly/binaryen) 将 [TypeScript](http://www.typescriptlang.org) (基本上是带类型的 JavaScript)的严格变体编译成 [WebAssembly](http://webassembly.org) 。它生成精益和平均的 WebAssembly 模块，而仅仅是一个`npm install`之遥。

### [关于](https://assemblyscript.org) [简介](https://assemblyscript.org/introduction.html) [快速入门](https://assemblyscript.org/quick-start.html) [开发说明](https://assemblyscript.org/development.html)

## 贡献者

[![Contributor logos](img/078a49b07397ff575b3d1e7b61be51a0.png)](https://assemblyscript.org/#contributors)

## 感谢我们的赞助商！

大多数核心团队成员和大多数贡献者都是在空闲时间做这项开源工作的。如果你使用 AssemblyScript 完成一项重要的任务或者打算这样做，并且你希望我们在它上面投入更多的时间，[请将](https://opencollective.com/assemblyscript/donate)捐赠给我们的[open collection](https://opencollective.com/assemblyscript)。通过赞助这个项目，你的标志将出现在下面。非常感谢你的支持！

[![Sponsor logos](img/229ffec963204f876b62fb4ed1efdd53.png)](https://assemblyscript.org/#sponsors)

</article>

[View on GitHub](https://github.com/AssemblyScript/assemblyscript)

出于我们今天的目的，我们将讨论我在开发`as-pect`测试命令行工具时遇到的一些问题。更具体地说，当我试图通过将*更多的*逻辑推入 Web 组装来使`as-pect`更快时，遇到了阻碍。

大家都知道 Web Assembly 是快速高效的字节码。因此，将应用程序移植到 Web 程序集应该是最佳解决方案。这是我开始时的*非常*的假设。

你看，`as-pect`在 JavaScript 中有所有的测试运行逻辑。更具体地说，它是从 TypeScript 编译的，并通过命令行使用`node.js`运行时来运行。当开始优化过程时，我注意到可以将所有的测试逻辑*转移到*web assembly 测试模块本身。

第一个直觉反应可能是，因为测试逻辑现在在 Web Assembly 中运行，所以事情会变得更快。

情况并非如此。

为什么不将测试框架移植到 AssemblyScript 中？想象一下，需要编译超过 25 个包含一些`describe()`函数调用的条目文件。出于报告和组织方面的原因，必须分别解释它们。更糟糕的是，WebAssembly 目前不支持函数的动态链接。因此，每个测试的二进制文件必须是静态链接的。那是什么意思？整个测试库的副本必须放入*每个*测试模块中。

这看起来没什么大不了的，但是`as-pect`本身在 25+ `.spec.ts`个文件的过程中运行了 200 多个测试。这意味着需要复制 25 份测试框架。这个框架总共需要编译、解释和引导将近 25 次以上。

现在从 JavaScript 的角度考虑一下。自举框架只需要解析、加载、执行和优化*一次*，然后框架就会加速`V8`完成它的工作。总之，在这种情况下，V8 是一个出色的动态解决方案。将测试逻辑转移到 Web Assembly 阻止了 JavaScript 所有经过实战检验的性能提升，换来的是更长的启动时间。

这不是唯一的问题。事实上，并不是每一个测试框架功能都可以在 web assembly 中描述。例如，获取当前时间必须使用`performance.now()`函数。在`node.js`还不支持`wasi`选项。

导入函数的数量可能减少了，但是对 JavaScript 的调用也增加了。当然，这些事情可以被优化，但是还有许多其他的小事情增加了“假优化”代码的味道。最终结果是可以衡量的，总体执行速度降低了大约 50%。这仅仅是因为我要求编译器利用更多的工作，而这些工作实际上是 JavaScript 主机的责任。

总的代价是测试速度增加了可怜的 10%,每个文件已经只有 25 毫秒了。这并没有让事情变得更快。这使得整个测试框架变得更糟。这是因为*编译时间*是瓶颈，*不是*测试。此外，这个方向会导致特征灵活性的问题。

花时间去想你可以把所有的问题都移植到 Web Assembly 是没有效率的。你可能正在制造你以前不必处理的问题。我知道 AssemblyScript 可以使您的类型脚本更快，但它并不总是解决方案。不要让这个事实阻止你尝试让你的软件变得更好。相反，让它引导你走上一条使用正确工具的道路。

例如，让`V8`优化您的 JavaScript 有时是您可以选择的最佳解决方案，因为 JavaScript 非常*灵活。写 JavaScript 的人都知道这一点。编写可以在`node.js`中立即执行的代码可以帮助您避开前面提到的缓慢的编译时间和繁琐的构建步骤，除非您正在用 TypeScript 编码。像添加和测试一个新的命令行界面选项这样的定性顶级任务将会发生得更快。有许多工具已经针对这种目的进行了单元测试和优化，因此，我们应该负责站在巨人的肩膀上实现我们更高的目标。*

这意味着你应该从 JavaScript 开始，使用编译器让你的小部分代码变得更快。如果你能测试你的软件的速度和功能，并且证明 Web Assembly *比*快，那就使用它。句号。不要回头看。然而，即使是最聪明的房屋建筑商也被告知在砍柴前要测量木材。你负责建造一座人们将要居住的房子，因此，你应该确保这项工作正确完成。

像聪明的建设者一样解决你的问题，而不是像热情的想法探索者一样。

快乐编码。
-jtenner