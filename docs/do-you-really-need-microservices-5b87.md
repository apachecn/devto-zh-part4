# 真的需要微服务吗？

> 原文：<https://dev.to/browserlondon/do-you-really-need-microservices-5b87>

十多年来，我们一直在[设计和构建](https://www.browserlondon.com/services/design-development/)软件，其中大部分时间我们都在使用出色的 Symfony 框架。Symfony 是一个传统的整体 PHP 组件集，大致受 Java Spring 的启发，我们发现它非常适合快速开发[企业 web 应用](https://www.browserlondon.com/case-study/insights/)和我们赖以生存的数字产品。

然而，去年 Symfony 4 的发布代表了框架焦点的逐渐变化；远离其单一的基础，走向[微服务方法](https://en.wikipedia.org/wiki/Microservices)，这是一种在过去几年中日益流行的方法。

为了说明这一转变，新版本被宣传为“默认为微”，Symfony 基金会大谈其新的[微内核](https://en.wikipedia.org/wiki/Microkernel)设计，声称与 Symfony 3 相比，启动应用程序所需的代码减少了 70%。

除其他优势外，这一变化意味着运行单个应用程序的开销要小得多，这使得 Symfony 在微服务架构中的使用更具吸引力。

## 什么是单片和微服务？

微服务设计的概念是将传统的大型应用程序分割成几个不同的小型应用程序。每个应用程序都将处理单一领域的业务功能，并与其他组件协作，就像它们是第三方应用程序一样。

[![Comparison diagrams of the microservices and monolith approaches possible with Symfony 4](img/561eae216f243c4c8cf723befb924201.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wQmlEKTj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/04/Monolith-and-Microservices-Comparion-1024x568.png)

现在，这真的是一个新事物吗，或者这只是[面向服务的架构](https://en.wikipedia.org/wiki/Service-oriented_architecture)的一个现代名称？好吧，我们不打算在这里争论——[Slashdot](https://slashdot.org/)和[黑客新闻](https://news.ycombinator.com/)毕竟可以很好地处理这个问题。我们要说的是，微服务方法(或者你想叫它什么)主要对大组织有益。这是因为非常大的应用程序可以分解成几个不同的服务，每个服务由他们自己独立的开发团队管理。

微服务架构的另一个好处是允许灵活扩展一个特定组件的容量，而不是整个应用程序。这非常适合弹性云计算的世界，但是在大多数情况下，我认为这种效率的提高被一个大的、突出的问题淹没了。

## 你真的需要微服务吗？

我的观点是，除非你为谷歌或网飞这样有数百名软件开发人员的公司工作，否则你可能不需要微服务。事实上，对于大多数中小型企业来说，采用这种设计路线可能是非常不合适的。

我将谈到一些例外情况，但是最大的问题是这种方法的开发和维护成本。我们可以通过一个简单的问题大致确定微服务是否应该成为您的起点:

> 你的系统中的一个单独的组件，比如用户管理，是否大到需要一个以上的开发人员全职不断地开发？

如果答案是否定的，微服务的方法可能是浪费时间和金钱。相反，如果你足够幸运，以后达到了那个规模，你可能有能力慢慢地把那些需要个人关注的部分分开。

## 为什么微服务的开发和管理成本更高？

单片应用程序通常是一个便宜得多的提议，因为你不需要处理大量的分布式系统问题。使用 Symfony 这样的框架的 Monoliths 提供了许多好处，它提供了开箱即用的集成特性，可以很容易地从应用程序的各个方面进行访问。您可以在很大程度上避免处理以下问题:

*   分布式系统上的认证和授权。
*   通过多个独立的系统跟踪复杂的交易问题。
*   分布式锁定。
*   服务之间的通信。
*   针对多个应用程序的附加配置管理。

## 规则的例外(又名，混合方法)

有些时候微服务是合适的，但根据我的经验，这些情况下扩展需求或容错需求超过了必须设计和管理分布式系统的缺点。一个很好的例子是像 [Monzo Bank](https://monzo.com/blog/2016/09/19/building-a-modern-bank-backend/) 这样的企业，他们需要能够立即根据需求进行扩展，并且能够确保系统中一个领域的故障不会影响到另一个领域。

我们在 Browser 重复了几次的一个好方法是采用混合方法进行系统设计。这涉及到一个由支持微服务包围的中心整体，但只有在有充分理由让它们存在的地方。例如，当[将 NLP 处理集成到应用](https://dev.to/browserlondon/natural-language-processing-here-s-how-it-works-and-how-it-saved-our-client-time-and-money-5e9g)中时，我们最近使用了这种方法。

[![Illustration of the hybrid approach between monolith and microservices approach possible with Symfony 4](img/874e85f4cda7c2f8a3d096c760ce9c5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4qIqp7Ip--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/04/monolith-microservices-hybrid-1024x579.png)

我们已经构建了几个系统，其中核心业务应用构建为一个整体(通常在 Symfony 中),繁重的数据处理由单独的微服务管道处理。这不仅使我们能够在不影响核心应用性能的情况下处理海量数据，而且我们可以在需要时将这些组件脱机，而不会影响平台的日常运营。

一种经过深思熟虑的方法——最好是对规模和未来发展需求有清晰的理解——对于架构决策至关重要。你想快速上市吗？要不要支持几百万用户？你需要[处理大量的数据流](https://www.browserlondon.com/blog/2017/07/13/divide-conquer-manage-multiple-data-streams/)吗？

在早期做出正确的决定会让你的产品有最大的机会在最短的时间内提供投资回报，而不排除你将来想要探索的选择。稍后计划将组件分离到微服务中通常比在最初的 MVP 开发中更具成本效益。

* * *

帖子[你真的需要微服务吗？](https://www.browserlondon.com/blog/2019/07/01/do_you_need_microservices/)首先出现在[浏览器伦敦](https://www.browserlondon.com)上。