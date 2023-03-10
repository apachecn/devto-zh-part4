# 短绒:改善你的工程团队的灵丹妙药

> 原文：<https://dev.to/cavezza/a-linter-the-magic-bullet-to-improve-your-engineering-team-222n>

linter 被正式归类为[静态代码分析](https://searchwindevelopment.techtarget.com/definition/static-analysis)工具。它通常是一个命令行工具，它解析项目的代码来寻找模式。然后，它会根据普遍接受的标准(或您认为重要的自定义规则)检查找到的模式。

linter 的主要目的是发现错误，找出 bug，并帮助在项目中实施标准化的编码风格。当你实现一个 linter 的时候，它会做更多的事情！

在 javascript 生态系统中，你最常看到的 linters 是 [TSLint](https://palantir.github.io/tslint/) 和 [ESLint](https://eslint.org/) 。两者都可以通过 [npm](https://www.npmjs.com/) 安装，可以让你的软件开发生命周期更快。

在这篇文章中，我将讨论为什么棉绒很重要。然后，我将展示棉绒的典型用法。

*注意:我最近的大部分作品都是棱角分明的。这篇文章中的所有例子都是通过角度命令行`ng Lint`* 来使用 TSLint

# 对于一个运转良好的工程团队来说，林挺非常重要。

## 它在你的代码中发现错误。

让我们看一个简单的例子。

这里有一个在编写代码时经常发生的情况:输入错误。快速看一下 IDE 中的 linter 是如何处理这个问题的。

<figure>[![](img/521e513a36908c7fb349cfed50df09c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p2yojIjT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ibuildmvps.com/wp-content/uploads/2019/08/Screen-Shot-2019-08-22-at-5.42.16-PM.png) 

<figcaption>常见林挺场景</figcaption>

</figure>

一个棉绒将很快抓住这一点，你不会再考虑它。因为错别字加了下划线，所以你可以很快地修改它，而且不到两秒钟。想象一个场景，你没有一个棉绒钩在你的想法上。你可以继续写代码，直到你想看看它在浏览器中是如何工作的(在这个例子中，让我们假设你没有做 [TDD](https://technologyconversations.com/2013/12/20/test-driven-development-tdd-example-walkthrough/) )。

如果您没有安装 linter，您可能会继续编写代码，直到您想要测试它为止。你可以上传你的浏览器，但它不会像预期的那样运行。也许约会看起来不像预期的那样。您可以运行调试器或控制台。记录一些项目以查看发生了什么。假设你花了不到 5 分钟的时间找到并修复这个 bug。也许需要 30 秒。不管怎样，如果你用了棉绒的话，这个问题可以在两秒钟内解决。这些小事在一天、一周、一个月、一年的过程中累积起来。

没有 linter，在测试中找到 bug 是最好的情况。最坏的情况是什么？也许，你根本没有注意到一个 bug，而这个 bug 已经被生产出来了。我可以很容易地想象出这样一种场景，您正在处理一个测试覆盖率有限或者没有测试覆盖率的代码库。

## 增加团队士气

你曾经有过被同事撕成碎片的拉取请求吗？这不是一个有趣的经历。

<figure>[![](img/1b0e1db3ceb76b6c6cc5601339b86df7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--feBGVejC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ibuildmvps.com/wp-content/uploads/2019/08/Screen-Shot-2019-08-25-at-2.09.13-PM-1024x214.png) 

<figcaption>拉要求无数的评论不利于一个工程师的自我</figcaption>

</figure>

这会伤害你的自信。如果工程师们对自己的评论过于鲁莽，有时会损害与同事的关系。

有时候，在一份有大量评论的简历中，工程师可能会忍住不去评论其他事情。

linter 通过减少与编码风格相关的注释、清除错误和 bug 来帮助缓解这些问题。当您将 linter 挂接到您的持续集成管道或持续部署管道(在本文的其余部分将被称为 CI/CD 管道)时，您会发现这些问题并在到达拉请求步骤之前停止构建。

由于拉请求现在无法打开这些风格问题或代码中的明显错误，这减少了工程师同事对拉请求的评论。

这有助于增加单个工程师打开 PR 的信心，并减少对其他工程师的怨恨。

## 它迫使一种有凝聚力的、一致同意的风格

假设你是一个初级开发人员，你刚刚打开了你的第一个拉请求。你为这一成就感到非常自豪！

大约一小时后，你得到了第一条评论。Veronica 建议您应该使用粗箭头函数，而不是传统的 javascript 语法。您渴望合并第一个 pull 请求，所以您很快做出了更改并将代码推回。

半小时后，你的公关又收到了一条评论。这一次，Michael 建议您刚刚更改为粗箭头函数的代码最好使用传统的 javascript 语法。

妈的。

初级开发人员经常会遇到这种情况。

工程师各种各样。他们来自不同的背景，过去使用过不同的语言。好的工程师都是固执己见的。好的代码库也是固执己见的。

一致认可的风格对现代团队来说很重要。你应该有一个受林挺规则支持的风格指南。如果你的团队喜欢胖箭头函数，确保这是一个林挺规则。如果它更喜欢传统的 javascript 函数，确保这是规则。

你的团队应该遵守这些规则，除非有一些好的理由去打破它。

这有助于代码的可读性、代码的可理解性以及初级工程师的体验。

## 它有助于创建更好的整体架构

既然 linter 已经发现了小错误和风格问题，那么团队中的同事就可以专注于代码中的其他问题了。

也许代码没有利用你的团队所采用的新架构。也许有一行效率低下的代码，在您扩展时需要很长时间才能执行。也许你用一种混乱的方式命名了一个函数，这可能会在将来绊倒其他人。

现在，这些风格问题、明显的错误和小问题已经在 linter 中被捕获，开发团队中的工程师可以专注于更深层次、更重要的问题。

这反过来有助于创建一个更好的整体架构和代码库。

# 在实践中，你通常会在三个不同的地方使用棉绒

这些都在这篇文章的前面提到过，但是我想确保我在这里明确地提到了它们。

## 在您的 IDE 中

当连接到您的 IDE 时，林挺错误会自动加下划线，这样您就可以在编写代码时实时看到它们。

<figure>[![](img/521e513a36908c7fb349cfed50df09c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p2yojIjT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ibuildmvps.com/wp-content/uploads/2019/08/Screen-Shot-2019-08-22-at-5.42.16-PM.png) 

<figcaption>常见林挺场景</figcaption>

</figure>

## 命令行中的

通过在终端中键入命令，您可以查看代码库中的所有林挺问题。在 angular 项目中，您应该键入`ng lint`。

[![](img/472b2fc4ad1b758a59d989aa5a9ee91d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ghxNpcTq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ibuildmvps.com/wp-content/uploads/2019/08/linting-errors-command-line-1024x826.png)

## 在您的 CI/CD 管道中

您的 CI/CD 管道将使用终端命令来运行管道中的 linter。然后，您可以设置您认为合适的规则，以便在超过某个阈值时使构建失败。

[![](img/2c9ace0004c92689ca23a40f40bf3464.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zmOBQn8C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ibuildmvps.com/wp-content/uploads/2019/08/linter_bitbucket_pipelines_failure.png)

[![](img/17a83464bc69494cea8ea9b94e13a251.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QvUBj9Z4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ibuildmvps.com/wp-content/uploads/2019/08/linter-bitbucket-pipelines-failure.png)

棉绒是工程队最好的朋友。它们使您的团队更快，它们使您的团队更快乐，它们提高整体代码质量，并且它们防止 bug 进入生产。

**定期收到我的电子邮件，里面有最好的在线资源和链接**
[https://tinyletter.com/rcavezza](https://tinyletter.com/rcavezza)

**在我的博客上阅读更多内容**

[http://ibuildmvps.com](http://ibuildmvps.com)

**联系我**

[http://twitter.com/cavezza](http://twitter.com/cavezza)

[http://linkedin.com/in/cavezza](http://linkedin.com/in/cavezza)