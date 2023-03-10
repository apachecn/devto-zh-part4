# 如何开发微服务:7 个事件驱动的考虑因素

> 原文：<https://dev.to/solacedevs/how-to-develop-microservices-7-event-driven-considerations-3gkp>

[![](img/f59b53af7fcc436dd36cb1932b520184.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o43JgGuW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://solace.com/wp-content/uploads/2018/12/advanced-event-broker.png)

在微服务应用开发方面，开发人员需要了解的最重要的事情是什么？许多组织迁移到微服务的原因只有一个:敏捷性。在一个竞争对手唾手可得、上市时间就是一切的世界里，以提供底线商业价值的方式快速创建和修改组件的能力是至关重要的。然而，作为组织中的开发人员，您也知道运营系统的性能至关重要，无论敏捷微服务如何帮助您的团队，您都有责任快速交付并确保运营系统能够在峰值负载下运行。为了实现敏捷性和性能，许多开发团队都在使用事件驱动的微服务。那么，想要成功，你需要知道些什么呢？

简而言之，你需要知道:

1.  信息传递的基础——它的好处和各种通信模式
2.  开发人员对分布式计算的常见错误假设
3.  事件驱动架构如何帮助您
4.  为什么为您的微服务选择编排而不是编排
5.  为什么你应该拥抱最终的一致性
6.  支持事件驱动的微服务开发的框架
7.  可视化和编排您的微服务的工具

<sub>**注:此博文最近更新于 2020 年 7 月 7 日**</sub>

## 1。消息传递的基础

在您的企业内利用微服务应用程序开发的优势需要对事件驱动的微服务的[消息传递模式以及何时使用它们有更广泛的理解。](https://solace.com/blog/messaging-patterns-for-event-driven-microservices/)

使用消息传递在微服务之间进行通信可让您实现以下优势:

*   简单、可扩展的连接
*   简单、高可用性
*   简单的生产者/消费者可伸缩性
*   发布/订阅、消息过滤、路由和扇出的启用
*   生产者和消费者的分离

要了解更多关于这些好处的细节，我建议你阅读[这篇博文](https://solace.com/blog/messaging-between-microservices/)。

## 2。分布式计算的错误假设

自从彼得·多伊奇和詹姆斯·高斯林提出分布式计算的 8 个谬误以来，已经过去了 20 多年，但是这 8 个错误的假设在今天仍然被那些不熟悉分布式应用的开发人员所采用。

所有八个谬误都是相关的，但我已经将微服务应用程序开发领域中特别重要的谬误加粗，因为你将每个微服务做得越小，你的服务数量就越大，服务数量越大，这些谬误对你的稳定性和用户体验/系统性能的影响就越大。

*   **网络可靠**
*   **延迟为零**
*   带宽是无限的
*   **网络安全**
*   拓扑不会改变
*   有一个管理员
*   **运输成本为零**
*   网络是同构的

挑战在于，微服务需要连接性/数据来执行它们的角色并提供商业价值，而数据获取/通信在很大程度上被忽略了，以至于工具已经严重落后。例如，API 管理/网关产品只支持同步的请求/回复交换模式，这与上面提到的谬误结合在一起，加剧了分布式计算的挑战。通过正确实施事件驱动架构，可以最大限度地减少这些挑战。

## 3。事件驱动的应用程序架构的优势

与同步和阻塞的面向服务的设计不同，事件驱动的设计是非阻塞的，并且使用大量的[回调](https://en.wikipedia.org/wiki/Callback_(computer_programming))。事件驱动设计并不是一个新概念，你甚至可以通过使用编程模式来熟悉它，例如[反应器模式](https://en.wikipedia.org/wiki/Reactor_pattern)或[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)。这些编程模式所使用的概念可以应用到使用架构模式的事件驱动的微服务架构中，例如[发布-订阅](https://solace.com/blog/publish-subscribe-messaging-pattern/)，以实现服务解耦、独立可伸缩性和一对多双向通信。

采用事件驱动思维的第一步是改变设计和架构解决方案的方式。最初的趋势是将服务之间的所有交互视为一系列请求/回复服务调用。事实上，如果你或你的团队使用“调用”、“请求”或“调用”这样的术语，那么这是一个明确的信号，表明你仍然在用请求/回复的范式思考。

相反，尝试这些:“我的服务应该处理哪些事件？”以及“我的服务将发出什么事件？”

## 4。微服务中编排的优势

一旦你采用了事件驱动的思想，你需要从[编排转变为](https://solace.com/blog/microservices-choreography-vs-orchestration/)。开发人员通常认为“服务 A 将调用服务 B，服务 B 将调用服务 C ”,然后通过一系列调用(a- > b > c)或通过创建一个 orchestrator 服务来实现该模型，例如 x- > a，然后 x- > b，然后 x- > c。当分布式计算开始时，这两种方法都会导致混乱，特别是当您开始扩展时。

[![orchestration vs choreography in microservices application development](img/044cfadfe488a571f06c27944972de47.png)](https://solace.com/wp-content/uploads/2019/08/dev-considerations-eda_image-1.png)

[编排与编排，来源:Thoughtworks](https://www.thoughtworks.com/insights/blog/scaling-microservices-event-stream)

另一种选择是遵循编舞的哲学。服务应该对其环境的变化做出反应，好处是巨大的:

*   更好的敏捷性:敏捷开发团队更加独立，受其他服务变更的影响更小。
*   **服务更小/更简单**:每个服务不需要对下游服务或网络故障进行复杂的错误处理。谁喜欢写错误处理？？？不是我！
*   **服务耦合少**:服务不知道其他服务的存在。
*   **支持细粒度扩展**:每项服务都可以根据需求独立扩展或缩减。这确保了良好的用户体验，并减少了计算资源的浪费。
*   **易于添加新服务**:由于耦合度较低，新服务可以上线、使用事件并实现新功能，而无需更改任何其他服务。

这些好处不是免费的；没有灵丹妙药…

然后，状态的一致性就成了关注的焦点，因为服务暂时关闭意味着事件状态的改变可能不会立即得到处理。从根本上说，我们如何处理这种负面的副作用？

## 5。微服务的最终一致性

最终一致性是指一致性将在未来发生的想法，它意味着接受事物可能会在一段时间内不同步。这是一种让开发人员避免使用讨厌的 XA 事务的模式和概念。事件/消息平台的工作是确保这些域更改事件在被服务适当处理和确认之前不会丢失。

有些人认为最终一致性的唯一好处是性能，但真正的好处是微服务的解耦，因为单个服务只是对他们感兴趣的事件进行操作。

## 6。事件驱动的微服务开发框架

作为一名开发人员，您希望能够轻松地编写干净的代码，使您能够更快地交付功能。因此，开发语言和相关的工具变得很重要。虽然事件驱动架构允许我们分离我们的微服务，并选择不同的技术来实现每一项服务，但建议选择最适合您团队技能的标准语言/框架，仅在必要时偏离。

对于许多开发团队来说，他们的技能通常包括使用 Spring 框架和 Java(以及其他 Spring 支持的、基于 JVM 的语言)的经验。这一经验使得 Spring Boot 成为组织中最受欢迎的微服务应用程序开发框架。它的采用持续增长，最重要的是，它通过 [Spring Cloud Stream 内置了对事件驱动的微服务的支持。](https://spring.io/projects/spring-cloud-stream)

Spring Cloud Stream 是一个框架，用于构建使用共享事件代理连接的高度可伸缩的、事件驱动的微服务(Solace binder 可从这里获得)。除了提供一个实现框架，Spring Cloud Stream 还为事件驱动社区定义了与供应商无关的术语，包括定义通过*通道*发送或接收事件的*源*和*接收*应用。

[![microservices application development](img/211e96d4418dbf4e8415bcebc2541354.png)](https://solace.com/wp-content/uploads/2019/08/spring-cloud-streams-architecture.png)

如果你感兴趣，我会在本文和视频的[中更深入地研究使用 Spring Cloud Stream 和 AsyncAPI 代码生成器创建微服务。](https://solace.com/blog/asyncapi-codegen-microservices-using-spring-cloud-stream/)

## 7。在事件驱动的流程中对微服务进行建模和可视化

如前所述，使用事件驱动架构编排微服务的好处之一是实现更少的服务耦合。但是这种好处也带来了一些挑战。具体来说，当您的生产者和消费者不关心彼此的存在时，在设计时为您的架构建模会变得很困难，在运行时理解什么事件在哪里流动甚至更难。

许多不同的组织以许多不同的方式尝试解决这一挑战，但直到最近还没有一个大众可用的全面解决方案。Solace PubSub+事件门户的引入解决了这些挑战，它允许您在设计时可视化、管理、*和*编排您的微服务，并提供允许您发现、审计和保护您的运行时环境的功能。

[![microservices application development event portal](img/62d802bab0d1615a028009f05031883b.png)](https://solace.com/wp-content/uploads/2020/03/site-software2-event-portal-datasheet-1.png)

阅读更多关于[为什么开发人员需要一个事件门户](https://solace.com/blog/why-developers-need-event-portal/)的信息，在这里我将详细介绍我在几分钟内创建一致且可扩展的微服务的步骤。

## 结论

虽然事件驱动的微服务最初可能看起来很困难，但它们是大多数微服务和 IT 战略的未来。遵循这些步骤，成为他们发展的忍者，让你的生活变得更容易和简单！

帖子[如何开发微服务:7 个事件驱动的考虑](https://solace.com/blog/microservices-application-development-considerations/)最早出现在 [Solace](https://solace.com) 上。