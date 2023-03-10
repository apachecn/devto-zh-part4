# 系列:如何利用微服务构建无服务器的 GraphQL API

> 原文：<https://dev.to/azure/series-how-you-can-build-a-serverless-graphql-api-leveraging-microservices-327k>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> 我已经写了一个由两部分组成的系列文章，解释了一个非常常见的情况。你有一个或多个服务，其中有一个团队维护每个服务，因此它有自己的生活。

有时你可能需要构建一个需要从这些服务中读取数据的应用程序。如果我们在这些服务前面有 GraphQL 之类的东西，这样新应用的构建者就可以只查询他们需要的数据，而不是进行一堆 HTTP 调用，这不是很好吗？GraphQL 最终使用这些服务的 URL，因为我们对它们进行了 Dockerize，因此我们在 GraphQL API 和服务之间有一个非常松散耦合的方法。它是松散耦合的，因为服务不知道 GraphQL API，而 GraphQL API 可以很容易地将一个数据源替换为另一个符合相同模式的数据源，毕竟，它只是一个 URL。

在本系列中，我假设我们不会经常使用 GraphQL API，因此让它拥有与其他关键业务服务相同的正常运行时间和成本是没有意义的。此外，GraphQL 可以被看作是对它获取的结果的计算，并不真的需要有自己的状态。因此，它是实现无服务器的理想选择。

这是两个部分:

*   [构建服务和 GraphQL API](https://dev.to/azure/learn-how-you-can-build-a-serverless-graphql-api-on-top-of-a-microservice-architecture-233g) 在这里，我们将展示如何构建服务，并使用 Docker 对它们进行容器化，从而使它们可以轻松地被推送到云中。我们还展示了如何引入 Dockerized 服务及其 URL，并构建我们的 GraphQL API
*   注册一个免费的 Azure 账户要创建无服务器的 Azure 功能，你需要一个免费的 Azure 账户
*   [迁移到云](https://dev.to/azure/learn-how-you-can-build-a-serverless-graphql-api-on-top-of-a-microservice-architecture-part-ii-deploy-it-to-the-cloud-26b6)在这里，我们将每个服务推送到云中的一个容器注册中心，我们还从每个服务创建了一个服务端点。我们还创建了一个无服务器应用程序和一个函数，并从中调用我们的 GraphQL API。然后，我们使用 VS 代码将无服务器应用程序带到云中。