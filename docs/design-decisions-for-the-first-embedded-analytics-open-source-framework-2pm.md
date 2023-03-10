# 第一个嵌入式分析开源框架的设计决策

> 原文：<https://dev.to/cubejs/design-decisions-for-the-first-embedded-analytics-open-source-framework-2pm>

在过去的几年里，我们一直在开发 [Cube.js](https://github.com/cube-js/cube.js) ，这是一个专门为**定制和嵌入**而构建的分析框架。数据工程师可以使用许多很棒的工具来构建内部数据基础设施。但软件工程师缺乏工具，他们正在构建面向生产和客户的应用程序，需要将分析功能嵌入这些应用程序中。

我们的主要需求是使它容易扩展到大型数据集，同时允许在前端进行完整的 UI 定制。它还必须是无依赖性的，尤其是不需要连接复杂的数据管道作业基础设施。

它已经在多家公司投入生产一年多了，数据集已经达到了 Pb 级。由于对 Cube.js 的工作充满信心，我们在 4 个月前开源了它，让更多的人使用。在这篇文章中，我想分享一个关于 Cube.js 架构的详细描述，以及我们为什么这样设计它。看完之后，我希望你能尝试一下 Cube.js，用于你自己的分析应用程序！

下面的模式显示了 Cube.js 通常是如何部署和嵌入到现有的应用程序架构中的。Cube.js 后端微服务连接到一个或多个数据库，负责数据库队列、数据模式、缓存、安全性和 API 网关。客户端从后端加载聚合数据，对其进行处理，并将其发送到您选择的可视化库。

[![](img/d29cbfef3686fb5e7eecd4ddec5e3a53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--02GqS3jJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p45f1anlsnzdg5vmuu4h.png)

下面我将详细描述后端和客户端到底发生了什么，但首先我想强调一下我们在设计 Cube.js 时做出的最重要的决定。

## 数据模式

ORM 在软件开发中无处不在，但是当涉及到分析时，它通常以大量的 SQL 脚本和模板而告终。数据模式背后的想法是采用 ORM 的最佳实践，并将它们应用到分析用例中。我们抓住了一个很老的多维分析的想法，将度量和维度作为抽象实体，事实上创建了一个 [ROLAP](https://en.wikipedia.org/wiki/Online_analytical_processing#Relational_OLAP_(ROLAP)) (关系 OLAP)引擎，它将度量和维度转换成 SQL 代码。

数据模式最重要的一点是我们使它完全动态化。Cube.js 中的数据模式不是类似 XML/JSON 的静态东西，而是 JavaScript 代码，这意味着您可以动态地生成它。您甚至可以在运行时从数据库或通过 API 加载度量或维度定义。

拥有合适的模式对于为 API 客户端提供抽象和灵活的查询语言至关重要。没有人想通过 API 发送 SQL 代码或 SQL 片段 id，因此在这种系统的每一种情况下，最终都会开发查询语言。这就是为什么我们已经让 Cube.js 自带了它，并由 OLAP 最佳实践提供支持。

[![](img/fb28ad218bf4d8218a0c25eb20440089.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VfgBrBUA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g32aix3um7q1llxebjyp.png)

## 在数据库预聚合中

虽然我们有一个通常的最后一英里内存缓存，但预聚合对性能有很大影响，特别是对于动态查询。Cube.js 可以在您的仓库中创建可重用的聚合表，读取速度极快。一个聚合表通常服务于多个查询。当新的底层数据进来时，Cube.js 还负责刷新聚合表。

Cube.js 还可以根据请求的度量和维度的模式自动计算和构建所需的聚合表。我们使用了数据立方体格的原理，我们将在以后的博客文章中讨论它背后的数学原理。此外，当所有原始数据都在 BigQuery 中时，您可以将聚合表存储在单独的仓库中，例如 MySQL。这将为您提供亚秒级的响应，由于 BigQuery 的设计，即使在小型数据集上这也是不可能的。

预聚合对于扩展至关重要。你可以把它想象成“ETL”中的“T”但是转换发生在您的仓库内部，完全由 Cube.js 协调。

[![](img/5ca2ff716c96e947cb21ceb45974c7d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gE-Ao-Gk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ka6392sqsb940yy16kro.png)

## 可视化不可知论者

这个属于“对功能说不本身就是一个功能”的范畴有很多好的成熟的可视化库，所以我们能做的最好的事情不是发明一个新的，而是确保我们能很好地使用它们。虽然 Cube.js 不呈现任何东西，但它提供了一组有用的助手，在从后端加载数据后对数据进行后处理。它包括诸如透视操作和填充缺失日期之类的事情。我将在稍后描述 Cube.js 前端客户端的组件时更详细地讨论它。

在这篇文章的剩余部分，我将介绍后端和前端的组件。

## Cube.js 后端

后端本身是一个 Node.js 应用程序，可以通过环境变量或为更复杂的用例编写一些 Javascript 代码来配置。它还需要一个数据模式，这是一个 JavaScript 代码，描述如何将度量和维度映射到 SQL 中。该架构还包含缓存、安全性和预聚合的规则。后端通常被部署为集群中的微服务。它有一个到所需数据库的连接，并向外部直接向客户端公开一个 API，或者如果您有客户端的代理，也可以在内部公开一个 API。

我们将 Cube.js 设计为一个模块化框架，包含 4 个主要组件。通常，它们都是一起使用的，但是您可以在您的特定用例中使用您需要的任何内容。下面我将概述后端的每个组件以及它所解决的问题。

### 模式编译器

*[Github 上的模式编译器](https://github.com/cube-js/cube.js/tree/master/packages/cubejs-schema-compiler)*

模式编译器编译一个数据模式，它是一个 JavaScript 代码，基于它，传入的查询生成一个 SQL 代码。然后，SQL 代码被发送到 Query Orchestrator，以便针对数据库执行。数据模式允许创建组织良好且可重用的数据模型。因为它是 JavaScript，所以您可以动态地创建所有需要的定义，将公共部分提取到助手中，并且通常应用最佳工程实践来根据业务定义组织数据。

模式也是定义行级安全性的地方。用户上下文可以随每个请求传递给 Cube.js，并传播到模式级别。在模式中，您可以使用用户上下文来限制特定用户只能访问特定数据。

预聚合是一种广泛用于大数据集的 Cube.js 特性，也在模式中定义。在这种情况下，Schema Compiler 生成的不是一个查询，而是一个相关查询的列表，以首先构建预聚合，然后生成获取数据的最终查询。

### 查询编制器

*[在 Github 上查询 Orchestrator](https://github.com/cube-js/cube.js/tree/master/packages/cubejs-query-orchestrator)T3】*

Query Orchestrator 的工作是确保数据库不会过载，并确保多阶段查询以正确的顺序执行和刷新。为此，它为预聚合和数据查询维护查询执行队列。队列是等幂的，这意味着如果有多个相同的查询进来，只有一个查询会针对数据库运行。查询由数据库驱动程序执行。到今天为止，Cube.js 支持[十多个原生数据库驱动](https://cube.dev/docs/connecting-to-the-database)和一个通用 JDBC 驱动。

对于多阶段查询(使用预聚合时总是如此)，查询本身由多个预聚合和获取数据的最终查询组成。Orchestrator 确保所有必需的聚合表都是新的，并且在最终查询之前就存在。如果聚合表不存在或过时，它将安排一个查询来创建或更新它。

[![](img/736481a623959f3209d0d695f83b7653.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DdpJ6wko--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qwsywvqv0jdb5klb3jo1.png)

聚合表的后台刷新被抽象为异步过程，可以在独立应用程序内部运行，也可以在无服务器模式下运行。它还允许您将后台处理提取到多租户集群中。

### API 网关

*[Github 上的 API 网关](https://github.com/cube-js/cube.js/tree/master/packages/cubejs-api-gateway)*

API Gateway 定义 API 端点来执行查询、加载元数据和检查模式编译器生成的 SQL。网关实现了一个长轮询幂等 API 来加载查询结果。这使得它能够容忍连接性问题，并保证响应不受请求时限的限制。

网关还负责身份验证和授权。默认情况下，Cube.js 中的安全性是用 JWT 令牌实现的。每个请求都用一个 JWT 令牌进行签名，该令牌可以选择包含要传递给数据模式的安全上下文信息。如果您想从数据库或特定微服务加载安全上下文，可以用定制中间件覆盖默认安全模型。

### 服务器核心

*[Github 上的服务器核心](https://github.com/cube-js/cube.js/tree/master/packages/cubejs-server-core)*

服务器核心将上述所有组件连接在一起，并公开一个配置入口点。

服务器核心可以嵌入到现有的 Node.js 应用程序中。要将 Cube.js 作为独立应用程序启动，需要使用服务器包；对于无服务器模式—无服务器软件包。

## Cube.js 客户端

*[Github 上的 Cube.js 客户端核心](https://github.com/cube-js/cube.js/tree/master/packages/cubejs-server-core)*

Cube.js 客户端是一个 JavaScript 库，它使用 Cube.js API 并对查询结果进行后处理。核心客户端是一个普通的 JavaScript 库，如果使用 React Native 之类的工具，可以在浏览器或移动设备上运行。此外，我们为 [React](https://github.com/cube-js/cube.js/tree/master/packages/cubejs-react) 、 [Angular](https://github.com/cube-js/cube.js/tree/master/packages/cubejs-client-ngx) 和 [Vue.js](https://github.com/cube-js/cube.js/tree/master/packages/cubejs-vue) 提供了包，以便于将 Cube.js 客户端集成到这些框架中。

Cube.js 客户端从 Cube.js 后端提取加载数据的传输层，并处理加载和错误状态。一旦加载了数据，客户机就提供一组助手方法来对数据进行后处理。客户端提供了一个透视界面，用于在图表或表格中显示数据；它还有元数据操作方法和一些实用程序，比如填充缺失的日期。

我们有意省略了可视化部分。我们从第一天起就有这个想法——建立一个框架来处理除可视化之外的所有事情。对于最终用户来说，这似乎是功能和定制的最佳组合。

## 生态系统

这些是 Cube.js 后端和前端的核心组件。但是作为一个围绕框架发展的社区，我很高兴看到生态系统是如何成长的。集成、教程和示例的生态系统和工具本身一样重要。我非常感谢我们令人惊叹的开源社区已经做出的所有贡献——数据库驱动程序、前端框架的绑定、教程和代码示例。

如果你对 Cube.js 感兴趣，并想做出贡献——我很乐意在我们的 [Slack 社区](https://slack.cube.dev/)见到你。一般来说，这是开始使用 Cube.js 并开始投稿的理想地方。