# API 的类型|什么是 API？|不同类型的 API

> 原文：<https://dev.to/decipherzonesoft/types-of-apis-what-are-apis-different-types-of-apis-3mjm>

自第一代计算机诞生以来，API 已经彻底改变了世界，但它们不是像今天这样用高级语言编写的。API 是编程的核心概念，每个程序员都必须学习以达到工作水平。到目前为止，API 已经达到了一个很高的复杂程度，需要对其进行分类，以便更好地理解。

# [什么是 API？API 的类型](https://www.decipherzone.com/blog-detail/Types-of-APIs)

API 是应用程序编程接口的首字母缩写，它表示允许创建访问其他应用程序、服务或操作系统的功能或数据的应用程序的一组功能和过程。

***文章来源:【https://www.decipherzone.com/blog-detail/Types-of-APIs】T2*T5】**

通俗地说，API 是连接应用程序的管道。这些连接是通过使用另一个应用程序的功能进入主应用程序来完成的。在 API 中，定义了将通过这些 API 进行通信的消息类型。

阅读:[什么是 API，它是如何工作的？](https://www.decipherzone.com/blog-detail/What-is-an-API-and-How-it-works-)

例如，许多网站显示来自 twitter 的直播提要，所以他们在自己的网站上使用来自 twitter 的 API 来获取和显示直播提要。

## [API 类型](https://www.decipherzone.com/blog-detail/Types-of-APIs)

原料药的分类可以根据以下三个参数进行:

***Web API 的所有权类型***

在所有权级别上，有四种主要类型的 API:

开放 API:这些 API 可以公开使用，就像 Google 的 Oauth APIs 一样，使用它们没有任何限制。因此，它们也被称为公共 API。

*合作伙伴 API*:访问此类 API 的特定权利或许可，因为它们不对公众开放。通常，这些类型的 API 与付费服务相关联。

*内部 API*:公司开发的用于内部系统的 API，这样可以提高开发团队的生产力，其中一个团队可以使用公司另一个项目的服务，称为内部 API。这些 API 也称为私有 API。

*复合 API*:流程和复合 API 都是一系列任务，但是复合 API 组合了不同的数据和服务 API。它是作为执行的结果而同步运行的任务序列，其中复合 API 的触发结果是执行的结果，而不是包含任务请求的执行结果的请求。它的主要用途是加速执行过程，提高 web 界面中监听器的性能。

***通信级 API***

*高级 API*

高级 API 是我们通常以 REST 形式使用的 API，程序员具有高度的抽象性，他们只关心执行有限的功能。

*低级 API*

低级 API 具有较低的抽象级别，因此它们是最详细的，这允许程序员在粒度级别上操纵应用模块内或硬件内的功能。通常，低级 API 用于发送实时视频或媒体源，以响应类似 Vulkan APIs 的触发器。

***Web 服务 API***

在 web 服务 API 中，根据构建 API 时使用的通信类型和行为方法进行分类:

*肥皂

*XML-RPC

*JSON-RPC

*休息

Web 服务 API 是使用互联网上的 URL 或网址向桌面、移动、web 应用程序和其他应用程序提供服务的小型应用程序。

**肥皂**

SOAP(简单对象访问协议)API 比它的同类产品更复杂。这些 API 有自己的通信协议，称为简单对象访问协议，这使得它们在安全级别和消息发送方式方面比 REST 更加明确。SOAP 为可靠的消息传递功能提供了全面的安全性、内置的 ACID(原子性、一致性、隔离性、持久性)遵从性和重试逻辑，这使得它更适合于处理银行交易、LDAP 交互等的企业应用程序。

阅读:[整体架构与微服务架构-利弊](https://www.decipherzone.com/blog-detail/Monolithic-vs-Microservice-Architecture)

SOAP 是一种标准化的协议，它只使用专有的 XML 格式来传输更具功能驱动性的结构化信息。API 用于发送有效载荷，即数据、数据类型和通信首选项。由于 SOAP 使用 XML 来发送有效载荷，因此它需要更多的带宽。除了 SSL 支持之外，SOAP 还使用 WS-Security，它在传输层上非常出色，比 SSL 更全面一些。由于高度的安全性，基于 SOAP 的调用不能被缓存。SOAP 与服务器紧密耦合，与服务器有严格的通信协议，这使得更改或更新变得更加困难。与 SOAP API 进行交互需要了解所有的知识，甚至在开始交互之前也是如此。

阅读:[顶级 Web 开发技术和框架](https://www.decipherzone.com/blog-detail/Top-Web-Development-Technologies-and-Frameworks)

SOAP 具有内置的 ACID 遵从性，它通过精确规定事务如何与数据库交互来减少异常并保护数据库的完整性。ACID 比数据一致性模型好得多。ACID 用于结构化数据库，如 Oracle 的 SQL 数据库，以处理大型电子商务平台中的银行交易和库存管理。

SOAP 内置了成功/重试逻辑，并通过 SOAP 中介提供端到端的可靠性。

您希望在构建应用程序时使用 SOAP API 的用例仅仅是希望实现高级别的安全性。

**XML-RPC**

XML-RPC(可扩展标记语言-远程过程调用)是一种使用特定 XML 格式传输数据的协议。XML-RPC 使用最小的带宽，并且比 SOAP 简单和古老得多。

**JSON-RPC**

JSON-RPC(JavaScript Object Notation)是一种使用 JSON 格式传输数据的协议。RPC 调用是微服务架构中服务用来通信的方法之一。

**休息**

REST(表述性状态转移)是一种更加数据驱动的架构风格，我们用它来构建 REST APIs。REST APIs 基于 URIs(统一资源标识符)HTTP 协议，并使用 JSON 作为数据格式，这是超级浏览器兼容的。与其他类型的 API 相比，REST APIs 易于构建和扩展。

* REST APIs 简化了客户端与服务器之间的通信。

* REST APIs 使用 SSL 安全性，这意味着它可以使用 HTTPS。

* REST APIs 使用不同的数据格式，包括纯文本、HTML、XML 和 JSON 发送有效载荷，这非常适合数据，并产生更多的浏览器兼容性。

*使用更简单的数据格式使得有效负载更轻，这使得 REST APIs 更适合更广泛的应用程序。

* REST APIs 使用单一的统一接口。这简化了应用程序与 API 的交互方式，因为它们需要通过相同的门户以相同的方式进行交互。这有利有弊。

*REST 调用可以缓存。

* REST APIs 对于可靠性和可伸缩性来说是最好的。

*您可能希望构建 RESTful 的原因是，它包括资源限制、更少的安全需求、浏览器客户端兼容性、可发现性、数据健康和可伸缩性。

阅读:[什么是无服务器 Web 应用开发？](https://www.decipherzone.com/blog-detail/What-is-serverless-web-application-development)

### **[这里有一些阅读整篇文章的加分信息](https://www.decipherzone.com/blog-detail/Types-of-APIs)**

***REST 中无状态是什么意思？*T3】**

在 REST(代表性“状态”传输)架构中，定义了服务器不在服务器端存储关于客户端会话的任何状态。这种限制被称为无国籍状态。从客户端到服务器的每个请求必须包含理解请求所需的所有信息，并且它不能利用服务器上存储的任何上下文。因此，会话状态完全保存在客户端。客户端负责在客户端存储和处理所有与应用程序状态相关的信息。这也意味着客户端负责在需要的时候向服务器发送任何状态信息。因此，服务器上不应该有任何会话关联或粘性会话。

***文章来源:【https://www.decipherzone.com/blog-detail/Types-of-APIs】T2*T5】**