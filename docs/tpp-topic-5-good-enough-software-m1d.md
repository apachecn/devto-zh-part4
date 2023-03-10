# TPP 主题 5:足够好的软件

> 原文：<https://dev.to/steadbytes/tpp-topic-5-good-enough-software-m1d>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/the-pragmatic-programmer-20th/topic-5-challenges/)

> 见实用程序员 20 周年纪念版系列[第一帖](https://dev.to/steadbytes/the-pragmatic-programmer-20th-anniversary-edition-series-1e2l)介绍。

## 挑战 1

> 看看你经常使用的软件工具和操作系统。你能找到任何证据证明这些组织和/或开发者乐于发布他们知道并不完美的软件吗？作为一个用户，你更愿意(1)等待他们把所有的错误都清除掉，(2)拥有复杂的软件并接受一些错误，还是(3)选择缺陷更少的更简单的软件？

虽然非常稳定，但 Ubuntu(以及任何 Linux 发行版)并不完美。在一个给定版本的整个生命周期中(LTS 版本或 Ubuntu 的临时版本),定期提供补丁/更新就清楚地表明了这一点。

理想情况下，我会选择选项 3，缺陷更少的更简单的软件，因为从复杂软件中清除所有的 bug(选项 1)即使不是不可能，也是极其困难的。软件越简单，就越有可能防止缺陷。此外，它遵循“做一件事并把它做好”的 Unix 哲学，这正是我尽可能追求的目标。许多常见的 Linux 软件工具都采用这种方法，并且缺陷很少(`grep`、`curl`、`wget`、`rsync`、`ssh`等)。

然而，有些软件(在我看来)天生就更复杂。一个像 Ubuntu 这样的操作系统必须包含许多不同领域的大量功能。在这种情况下，我更喜欢选项 2 **，前提是**软件背后的组织/开发者修复用户发现的漏洞。由于选项 1 对于复杂的软件来说基本上是不可能的，发布它“足够好”,然后随着时间的推移逐步改进它是一个更加*实用的*方法。

## 挑战 2

> 考虑模块化对软件交付的影响。与设计为非常松散耦合的模块或微服务的系统相比，将一个紧密耦合的整体软件块达到所需的质量需要更多还是更少的时间？每种方法的优缺点是什么？

在我给出答案之前，我想强调一下这个挑战中的一个微妙之处。它要求比较**紧密耦合的**单片软件和**非常松散耦合的**模块或微服务，而不是一般的单片与微服务架构*。*

 *我对此的回答取决于*所需质量*的定义和软件的范围。如果所需的质量指的是一个完全工作的、无缺陷的**第一版本**，那么紧密耦合的整体方法可能会在更短的时间内交付，因为需要管理的“移动部分”更少，并且识别系统的有界上下文并相应地将其拆分所需的时间也更少。然而，正如我们所知，软件会随着时间的推移而发展和变化。如果*要求的质量*是指在软件发展的生命周期中**保持的**质量水平，那么松散耦合的模块/微服务方法将花费更少的时间。这种方法(理论上)允许在不影响软件其余部分的情况下改变、替换、删除或添加功能的单个组件——允许更快地交付所需质量的软件。

如果软件的范围有限，那么将其分割成松散耦合的微服务所花费的时间可能会比开发速度的潜在增长更长。另一方面，对于大型复杂的软件，开发速度的提高可能会超过最初的“投资”,因为单个组件可以很容易地单独开发和测试。

## 挑战 3

> 你能想到受功能膨胀困扰的流行软件吗？也就是说，软件包含的功能比您可能会用到的要多得多，每一个功能都为 bug 和安全漏洞带来了更多的机会，并使您使用的功能更难找到和管理。你自己有掉进这个陷阱的危险吗？

我认为 [Steam](https://store.steampowered.com/) 遭受了一些功能膨胀，随着时间的推移，它已经发展成为一个商店、社交网络(社区、消息等)、开发平台(Steam Workshop)和游戏启动器。我并不是说 Steam 是软件的一个坏例子——我和许多其他人使用它取得了巨大的成功。然而，它也不知道是错误免费的任何手段！

我认为每个主要的现代软件项目都有功能膨胀的危险。我们生活在一种提倡“不前进就是后退”的文化中，这不可避免地导致新的特性/功能被添加到软件项目中。我不希望暗示持续改进是不好的，但是对于软件来说，增加系统的特性集和导致系统臃肿之间有一条细微的界限。作为工程师，我们应该意识到软件项目中“东西”的数量，并考虑创建额外的软件来提供新的特性是更合适的(再次回到 Unix 哲学)。Facebook Messenger 就是一个有趣的例子。脸书在 2008 年引入了脸书聊天，作为主要的脸书应用程序的一部分。随着聊天功能集和使用的增长，它在 2011 年被拆分成一个独立的 iOS/Android Facebook Messenger 应用程序，随后又推出了一个单独的网络应用程序。这减少了核心脸书应用程序的膨胀量(虽然这并不是说脸书不膨胀，但那是另一个讨论)。*