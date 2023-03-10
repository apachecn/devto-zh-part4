# 您现在应该学习的编码实践，并且永远使用它

> 原文：<https://dev.to/tracycodes/coding-practices-you-should-learn-now-and-utilize-forever-1eac>

这里有 5 个我在过去几年中了解并喜欢的编码实践，我每天都感谢自己。

# 1。标准代码格式

大多数代码库都是现成的，而不是编写的。与团队中众多贡献者的不同风格的代码相比，格式一致的代码库更容易阅读。标准化的格式带来了能够轻松地浏览代码并挑出你需要的部分，而不用把你的头往桌子上撞。漂亮+ ESLint +一个流行的林挺配置可以为你做所有繁重的工作。你有一个团队成员绝对拒绝在他们的 JavaScript 中使用分号吗？那也行！只是让他们用一个预先确定的配置来使用更漂亮的 ESLint，如果他们不遵循代码指南，这将返回错误。当我们在我们的代码库中实现这个精确的设置时，我们在代码审查和 QA 期间结束了许多讨论/激烈的争论。

# 2。如果没有意义，就不要干代码

DRY(不要重复自己)原则几乎是开发人员的口头禅(有时是一个很棒的面试问题)。但是，如果在不知道*为什么*的情况下应用它，那么它将导致一些抽象代码，这些代码简直难以阅读和理解。

对我和我的团队来说，我们坚持“最多两份”的原则。这意味着任何函数/代码都可以在一个代码库中复制两次。如果它需要被复制两次以上，那么我们进行重构，这样代码就可以在应用程序的其余部分完全重用，而不必不断地复制+粘贴代码。乍一看，这让我们无法假设两个问题应该以相同的方式解决。

# 3。通过日志记录调试代码

您应该在本地环境中通过日志而不是调试器来练习调试代码。对我们来说，我们在生产中使用 [Sentry.io](https://sentry.io) 作为我们的记录器/调试器。正因为如此，我们按*【每事件】*收费，这意味着日志和事件非常珍贵。

因为我们这样对待日志和事件，所以我们首先在本地机器上进行调试，以确保日志被添加到正确的位置。这反过来使得如果一个异常是通过 Sentry 处理的，那么跟踪起来非常容易，因为我们没有疯狂混乱的日志记录。

**记住，太多日志记录===根本不记录**

# 4。不要过早增加复杂性

不要用没有用户或利益相关者要求的特性来复杂化你的代码库。这是一个在早期产品生命周期中需要特别避免的问题。当我第一次加入我的新团队时，我一直在为此挣扎。我试着走出大门，摇摆着，超越期望。你知道这做了什么吗？它增加了更多的问题，更多的罚单，甚至给我的团队带来了更多的工作。

这是一种反模式。添加不必要的特性会使你的代码更难阅读和调试。当你让新的开发人员加入时，他们会发现很难区分重要的功能和凌晨 4 点添加的功能，这些功能是由过量摄入咖啡因造成的。

# 5。在项目生命周期的早期设置 CI/CD 管道

这话我怎么说都不为过。即使你独自进行一个项目，CI/CD 管道也会减少记忆(和执行)构建和部署代码库的开销。

一个常见的误解是 CI/CD 管道只对每天将大量代码推/部署到产品中的团队重要。从我的经验来看，即使你一周生产一次，也能节省*小时*。最近，我为我们的主项目引入了一个 CI/CD 管道(部署通常需要几个小时，因为很多工作都是复制+粘贴代码或将代码上传到服务器，并通过 diffing 替换旧文件)。引入 CI/CD 后，我们可以在不到 5 分钟的时间内运行完整的生产部署，并且如果出现错误，可以在几秒钟内回滚。

附注:拥有 CI/CD 管道通常会导致拥有准确的部署历史记录，并将其与特定的提交相关联。这有助于我们在事后分析中找到事故的根本原因。