# 整体架构与微服务架构——利弊

> 原文：<https://dev.to/decipherzonesoft/monolithic-vs-microservice-architecture-pros-and-cons-2n6l>

正在开发的大型企业应用程序给软件开发人员带来了太多的麻烦。这个问题根本没有解决方案，这就是为什么需要不同的建筑风格。因此“微服务”这个术语是在云计算领域可持续发展之后出现的，它是由 Peter Rogers 博士在 2005 年一次关于云计算的会议上首次使用的。

文章来源:[https://www . decipherzone . com/blog-detail/Monolithic-vs-Microservice-Architecture](https://www.decipherzone.com/blog-detail/Monolithic-vs-Microservice-Architecture)

“微服务”在 2011 年的一次软件架构师活动中首次亮相，当时该术语被用来描述许多与会者当时正在尝试的一种架构风格。

整体架构是我们通常使用的传统风格，而微服务架构仅用于无法承受停机时间的超大规模应用。

# [什么是单体建筑？](https://www.decipherzone.com/blog-detail/Monolithic-vs-Microservice-Architecture)

*Web 应用通常由三部分组成*

前端客户端应用程序是用 JavaScript 等语言编写的。

后端服务器端应用程序，包含用 java、PHP、Python 或其他语言编写的业务逻辑。

整个 web 应用的数据库

所有这些部分都是紧密耦合的，并且经常相互通信。因此，整个 web 应用程序就像一个整体，每个部分都相互依赖。

check out:[Web 应用程序开发流程如何变得更加高效？](https://www.decipherzone.com/blog-detail/How-Web-App-Development-Process-is-Becoming-More-Efficient)

# [单片架构的优势](https://www.decipherzone.com/blog-detail/Monolithic-vs-Microservice-Architecture)

与微服务架构相比，整体架构的代码结构较小。因此，基于整体架构的 web 应用程序易于开发、易于测试、易于部署和易于扩展。

# [单片架构的弊端](https://www.decipherzone.com/blog-detail/Monolithic-vs-Microservice-Architecture)

整体架构的复杂性随着规模的增大而增加，这使得这种方法仅限于特定规模的项目。

web 应用程序大小的增加增加了启动时间。

更大的 web 应用程序变得更加复杂，导致代码可读性降低，开发和调试困难。

一部分代码的更改可能会对代码的其余部分造成意外的影响。

集成新代码需要大量的测试和调试。

持续集成和持续部署变得困难。

如果 web 应用程序的一部分关闭，那么 web 应用程序的其余部分也会关闭。

# [何时使用单片建筑？](https://www.decipherzone.com/blog-detail/Monolithic-vs-Microservice-Architecture)

单片架构可用于不需要实时响应的项目中，它们可以承受停机时间。这类项目被限制在一定的规模内，如果 web 应用程序的规模预计会超过这个范围，那么就应该采用微服务架构。我们通常使用的大多数 web 应用程序都是基于单一架构的。

什么是 API，它是如何工作的？

# [什么是微服务架构？](https://www.decipherzone.com/blog-detail/Monolithic-vs-Microservice-Architecture)

在微服务架构中，大型 web 应用程序被划分为较小的服务，每个 web 服务负责执行一组特定的功能。如果我们以一个巨大的电子商务平台为例，那么该 Web 应用程序可以划分为如下较小的应用程序:

产品搜索应用程序

库存管理应用程序

产品选择和购物应用程序

付款申请

将交货分配给物流合作伙伴

使用数据分析和机器学习的追加销售应用程序。

从上面的例子可以明显看出，微服务架构的执行是复杂的，需要大量的规划。

Checkout: [什么是无服务器 Web 应用开发？](https://www.decipherzone.com/blog-detail/What-is-serverless-web-application-development)

微服务架构不仅仅局限于编码，它还需要在云平台上进行精心规划的部署。较小的应用程序部署在不同的服务器实例、共享服务器的主机上，并根据 web 应用程序的要求充当服务平台。

# [微服务架构优势](https://www.decipherzone.com/blog-detail/Monolithic-vs-Microservice-Architecture)

当大型 web 应用程序被分解成较小的服务时，会增加代码的可读性。

较小的服务易于开发、集成、测试和调试。

较小的服务可以由不同的团队根据他们选择的技术栈独立开发。

更容易的持续集成和持续部署。

对代码集成的意外影响大大减少了。

代码集成会产生较少的错误和资源冲突。因此，测试和调试的成本降低了。

由于经常使用的代码可以作为服务平台部署，该服务将提供几乎零停机时间，并具有处理异常请求高峰的灵活性。

分布式部署消除了购买昂贵的服务器主机和资源的需要。

# [微服务架构的弊端](https://www.decipherzone.com/blog-detail/Monolithic-vs-Microservice-Architecture)

为了构建基于微服务架构的 web 应用，需要经验丰富且昂贵的资源。

构建基于微服务的 web 应用需要 web 开发人员、云架构师、DevOps 工程师、质量分析师、项目经理、业务分析师、产品经理和许多其他团队成员，这些都在一个可扩展的敏捷框架中定义。

微服务架构增加了分布式系统的复杂性。web 开发人员必须编写代码来处理在使用分布式系统时出现的部分代码故障、服务故障和差异。

实现基于消息传递或 RPC 的进程间通信机制的开销。

测试微服务是典型的，因为为了测试依赖于其他服务的服务，开发人员和质量分析师必须首先运行所有的基础服务。

如今，Kubernetes 和 Dockers 被用来提供服务。

基于微服务架构的 web 应用中的每个服务都使用不同的数据库。因此，对于开发人员来说，反映不同数据库的公共列的变化是有开销的。

当服务在其他服务中被广泛使用时，对服务进行更改会变得非常困难。

# [什么时候使用微服务架构？](https://www.decipherzone.com/blog-detail/Monolithic-vs-Microservice-Architecture)

微服务架构应该仅用于需要实时响应的大型项目，或者无法承受停机时间并且在某个时间点可能出现异常用户高峰的项目。运行电信、电视网络、拼车应用、送餐应用和大型电子商务平台的项目通常基于微服务架构。

文章来源:[https://www . decipherzone . com/blog-detail/Monolithic-vs-Microservice-Architecture](https://www.decipherzone.com/blog-detail/Monolithic-vs-Microservice-Architecture)