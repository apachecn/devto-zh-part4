# 十二因素应用程序:回顾与展望

> 原文：<https://dev.to/heroku/twelve-factor-apps-a-retrospective-and-look-forward-4j4f>

如果你的团队正在为云创建应用，那么[十二因素应用方法论](https://12factor.net/)很有可能已经影响了你正在使用的框架和平台。流行的框架，如 Spring Boot、Magento 和 more，将这十二个因素作为他们设计的一部分。Heroku、Amazon 和 Microsoft 等领先公司使用并推荐这种方法。虽然每个月都会发布新的框架和方法，但很少有像这次这样影响深远的。

让我们来看看这些因素是怎么回事，七年前创造这种方法背后的故事，以及为什么它们在今天同样重要。

## 十二要素应用的创建

2011 年末， [Heroku](https://www.heroku.com/) 联合创始人亚当·威金斯(Adam Wiggins)知道应用程序开发和部署的状态存在问题。Adam 和他的团队已经亲自参与了数百个应用程序，并见证了数十万个应用程序通过 Heroku 平台部署到云中。这些应用中有一些是成功的，利用了分布式架构等特性。但是其中一些应用存在严重的问题:它们不可扩展，不可移植，或者不容易维护。该团队认识到这些问题应用程序中的一系列常见问题，并希望对此采取一些措施。

Adam 和他的团队提出了一套构建成功云应用的指导原则，这些原则将最大限度地减少成本和时间，最大限度地提高可移植性，实现持续部署，并在不改变流程或架构的情况下进行扩展。

## 十二要素应用:SaaS 应用开发的方法论

我们先来概述一下这十二个因素。你可以在[12factor.net](https://12factor.net/)看到每个因素的完整列表和额外细节。

1.  [代码库](https://12factor.net/codebase)
    使用源代码控制。每个应用程序一个代码库。部署到多个环境。

2.  [依赖关系](https://12factor.net/dependencies)
    声明并隔离依赖关系。永远不要依赖系统包的存在。永远不要在代码库中提交依赖项。

3.  [配置](https://12factor.net/config)
    将配置与代码库分开。

4.  [后台服务](https://12factor.net/backing-services)
    将应用消费的服务(数据库、缓存等)视为可附加的资源。您应该能够在不更改代码的情况下交换数据库实例。

5.  [构建、发布、运行](https://12factor.net/build-release-run)
    部署应用分三个独立的步骤:构建(将代码库转换成可执行文件)、发布(将构建工件与配置结合起来创建一个发布映像)和运行(每次启动时使用相同的发布映像)。

6.  [进程](https://12factor.net/processes)
    进程应该是无状态的。

7.  [端口绑定](https://12factor.net/port-binding)
    通过端口绑定输出服务。应用程序应该是完全独立的。

8.  [并发](https://12factor.net/concurrency)
    通过将应用程序分解成完成特定工作的单个进程来进行横向扩展。

9.  可弃置性
    应用程序应该能够快速启动，对故障具有弹性，并且能够优雅地关闭。预计服务器会出现故障、被添加、改变。

10.  [开发/生产对等](https://12factor.net/dev-prod-parity)
    保持开发环境与所有其他环境相同。

11.  [日志](https://12factor.net/logs)
    将日志视为事件流。写入 stdout 和 stderr。

12.  [管理进程](https://12factor.net/admin-processes)
    将管理任务(数据库迁移、后台作业、缓存清理等)作为一次性的独立进程运行。

## 十二要素应用如何改变应用开发和开发运营

在过去的七年里，这十二个因素已经引导了成千上万的应用程序走向成功。事实上，这种方法在创建可维护、可扩展和可移植的应用程序方面非常有效，以至于 12 个因素中的许多现在已经被采纳为云开发和开发运营的“常识”。我们现在都知道了显式依赖声明、向外扩展与向上扩展，以及代码库的好处。然而，这些因素中的一些在首次引入时是激进的建议。

这些久经考验、行业认可的因素甚至已经被一些最成功的云框架和工具所编纂。大多数现代框架，如 [Spring & Spring Boot](https://spring.io/blog/2016/09/12/springone-platform-2016-replay-12-factor-or-cloud-native-apps-what-exactly-does-that-mean-for-spring-developers) 、 [Symfony](https://symfony.com/blog/new-in-symfony-3-2-runtime-environment-variables) 和 [Magento](https://devdocs.magento.com/guides/v2.3/config-guide/deployment/pipeline/) (Adobe)都将这十二个因素作为其设计原则的一部分。诸如 Docker images 和 Heroku slugs(构建/发布/运行)、vagger(开发/生产奇偶校验)、Puppet 和 Vault(配置)以及 Papertrail(记录)等工具已被创建，以使用这些因素来实施、自动化和简化对应用的管理。

Heroku 平台也体现了十二要素。例如，Heroku 要求将应用程序分解成一个或多个轻量级的离散容器(dyno)——这是无状态因素的直接表现。Heroku 还强制语言和框架使用明确的应用依赖列表(如 Ruby 的 bundler)，允许使用一次性 dyno 隔离运行管理进程，并聚合应用程序中所有运行的 dyno 的输出流，以便日志可以作为流进行处理。

## 今天和七年前一样重要

十二因素应用程序方法论今天仍然和它最初发布时一样重要。去年有数百万人访问了十二因素应用网站。像[亚马逊](https://aws.amazon.com/blogs/compute/applying-the-twelve-factor-app-methodology-to-serverless-applications/)、[微软](//docs.microsoft.com/en-us/dotnet/standard/modernize-with-azure-and-containers/modernize-existing-apps-to-cloud-optimized/what-about-cloud-native-applications)、 [IBM 和 Pivotal](https://sdtimes.com/webdev/twelve-factor-app-methodology-sets-guidelines-modern-apps/) 这样的公司继续使用和推荐这种方法。

较新的架构，如微服务、无服务器容器和大多数云部署仍然受益于(并且经常严格执行)该方法。例如，Amazon Lambda 函数强制实施无状态组件、向外扩展、可处置性、隔离管理功能和支持服务等因素。

多年来，十二因素应用程序方法已经指导了大量的应用程序、框架和平台走向成功。在设计过程的早期考虑这些因素将有助于你和你的团队构建可伸缩、可移植、可维护的应用程序。