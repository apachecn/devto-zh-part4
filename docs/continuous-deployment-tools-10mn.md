# 持续部署工具

> 原文：<https://dev.to/scoutapm/continuous-deployment-tools-10mn>

*这篇文章最初出现在[童子军博客](https://scoutapm.com/blog/continuous-deployment-tools)上。*

在过去的十年中，软件开发发生了迅速的变化。许多公司已经从传统的瀑布开发模型转移到敏捷方法，这意味着拥抱持续集成和持续交付实践。但是，如果通过持续部署更进一步呢？您是否在没有任何人工干预的情况下自动部署到生产环境中？我们日常依赖的一些主要产品是。我们看看一些最好的持续部署工具，并把它们放在一起。

## CD，CI & CD 有什么区别？

在我们开始讨论持续部署工具之前，让我们花一点时间来澄清您可能对这三个相似且相关的缩写词有任何混淆:CI、CD，还有 CD！

持续集成(CI) 鼓励所有开发人员频繁地将他们的代码变更签入到一个主分支中。它旨在避免大型团队在跨多个版本和分支工作时试图集成代码时所面临的挑战。其思想是，当开发人员打开一个拉请求时，自动化系统可以触发一个构建过程，该过程运行系统的所有测试，然后部署一个类似生产的系统进行检查。这个想法是，这些系统尽可能地类似于产品，当开发人员试图一次合并太多代码时，他们可以避免在“集成地狱”的情况下浪费时间。

**连续交付(CD)** 是一种软件开发方法，诞生于敏捷软件开发运动。持续交付的目标是你的软件总是处于可部署的状态，随时可以发布。小而简单的版本被迅速地推出给用户，收到关于这些变化的反馈，然后循环重复。这与软件开发的传统瀑布模型形成了鲜明的对比，在瀑布模型中，开发以缓慢渐进的方式从一个团队流向下一个团队。随着连续交付，自动化被用来简化这一过程，但生产系统的部署是由**手动**完成的。

[![](img/d3dd497f308453ed2effa0fb781ed2c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JEwF5Fnv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ty7x9j8pqwgtx9ni1t7.png)

最后，**持续部署(还 CD！！)**然后，是团队实现这种持续不断交付的另一种方式。当某个步骤(比如合并到主分支)发生时，**会自动**部署到生产中，从而使流程更进一步。这一切都是在没有任何人类互动的情况下发生的。许多团队使用连续交付和连续集成，但是没有采用连续部署，而是更喜欢手动按下部署按钮。但是这种情况也在改变，因为现在许多公司更进一步，通过在他们的工作流程中使用持续部署，将他们的客户作为最新功能的测试者！

让我们总结一下当今最流行的持续部署工具，看看它们能为您提供什么。

## 詹金斯

让我们从市场上最流行的 CI/CD 工具 Jenkins 开始。Jenkins 将自己标榜为一个开源的持续集成服务器，拥有超过 1000 个插件，这使得它可以适应许多不同的用例。例如，如果您想采用持续部署方法，那么一个流行的途径就是蓝海插件。

[![](img/b7aeade15dc9f3766876c649e94e11e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Owee-t0G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4si6q3sulpoo9dy8ko1l.png)

由于是免费和开源的，如果你担心其他解决方案的成本，Jenkins 是一个不错的选择；另外，它的使用非常广泛，所以有大量的插件和文档可用。

## 竹子(阿特拉斯)

Bamboo 是来自 Atlassian 的持续集成和持续部署服务器解决方案，Atlassian 是 Bitbucket(源代码控制管理)和吉拉(问题跟踪)的幕后公司。如果你已经在使用 Bitbucket 和吉拉，那么与 Bamboo 的集成会非常方便。

[![](img/6fe717170afadc9c45b53592fb682fcf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ABoXLdkz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l4n57m2750cye4pmx7fp.png)

基本计划的价格从每月 10 美元起，但最便宜的专业级计划从每月 1100 美元起，并根据您需要的构建代理数量进行调整。

## 团队城市(Jetbrains)

TeamCity 是由流行的 IDE 开发者 JetBrains 提供的持续集成和持续部署解决方案。它有一个光滑易用的界面，充满了有用的功能。

[![](img/396d6c0f44da8e4f17245932d2b2c75d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2KmAmrVC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/umwfqieunmkn6b2wipo9.png)

一个免费的“专业服务器许可证”包括 3 个构建代理对小团队来说可能就足够了。之后，企业许可证从 1999 美元起。

## 章鱼调遣

与该领域的大多数竞争对手不同，Octopus Deploy 只关注事物的持续部署方面，而将持续集成方面留给其他软件解决方案。

[![](img/2185944b0f8b99413dac4663136b0dc2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Lfx7oC---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/felqgyq6wtg2eopg6qw8.png)

传统上，Octopus Deploy 只为。NET 世界，但它已经扩展，现在支持相当数量的其他语言。最便宜的云计划是 45 美元起。

## Gitlab

GitLab 为持续集成、交付和部署提供了一个完整的解决方案，所有这些都在一个与其 Git 源代码控制系统集成的界面中。这是一系列令人印象深刻的工具，类似于 Atlassian 的 Bamboo，如果您已经在使用 GitLab，那么 all in one 集成过程将是一个重要的决定因素。

[![](img/1ad75a913d6defbffad28a3950c700c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9IvoesT_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mlq6jcoz1r2xe8iw0yij.png)

与竞争对手相比，GitLab 的价格也非常合理。你可以选择自己托管或者使用 GitLab 的 SaaS，价格从免费到 99 美元不等。

## 部署机器人

与我们在这里提到的许多其他选项相比，DeployBot 似乎是一个更加简单易行的解决方案。你所需要做的就是注册一个帐户，连接你的源代码库和托管解决方案，然后你就可以开始手动或自动部署了。

[![](img/48fe898d25101679c7dc217e86b27ca4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LXbyMvUy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f7ioj0zcr3l5l1je0r4s.png)

虽然我们在谈论简单，但定价也很简单！有一个免费的基本(15 美元)、附加(25 美元)和高级(50 美元)计划，以满足广泛的需求。

## 总结

如您所见，2019 年有许多选项可用于持续部署。此外，这些选项经常与持续集成选项重叠，造成非常混乱的局面。什么最适合您将取决于许多因素，如成本、功能以及它如何与您的堆栈中的其他软件相适应。我们在下表中为您总结了要点。

[![](img/4094f941c5f3bb1639291ae3157783a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---gtb8fz8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m2zlj078k2z7h91xttqd.png)