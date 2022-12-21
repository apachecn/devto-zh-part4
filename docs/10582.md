# 我对 GraphQL 的体验

> 原文：<https://dev.to/ruairidhwm/my-experience-with-graphql-1d44>

# 什么是 GraphQL？

GraphQL 被描述为“API 的查询语言”。它是由脸书在 2012 年创建的，用于他们的移动应用程序，但在 2015 年被开源，此后越来越受欢迎。

GraphQL 是 REST 架构的替代方案，它允许用户指定他们想要接收的数据，而不是 REST 端点简单地返回所有内容。

返回不必要数据的端点被称为“过度提取”,会导致请求超过实际需要，前端需要解析它不会使用的数据，并且客户端可能需要执行多个网络请求，直到它获得所需的所有信息。

例如，比较以下两个查询:

*休息查询*

```
GET /users/1
{
  name: "John Doe",
  email: "john@test.com"
  // ...more user information
} 
```

现在，如果我们还想检索这个用户的评论，我们可能需要执行*另一个*查询，然后才能获得我们需要的所有信息。类似于:

```
GET /users/1/comments
{
    {
        title: "I really don't like too many requests"
        body: "REST is great but sometimes makes me have to fetch a lot of data"
    },
    {
        title: "I wonder if there's another way of doing this?"
        body: "Surely this is a problem people have encountered before?"
    }
  // ...more user comments
} 
```

*GraphQL 查询*

在 GraphQL 中，事情要简单得多。我们可以这样做:

```
query {
  User(id: "1") {
    name
    email
    comments {
      title
      body
    }
  }
} 
```

上面的代码将返回一个包含所有信息的对象。它的效率要高得多，而且我们不需要接收所有的字段，如果我们查询一个 REST 端点，我们就需要丢弃这些字段。

相反，我们也不会*欠蚀刻*。这是当一个端点没有给我们足够的信息，所以我们必须作出 *n* 额外的请求，以获得我们需要的一切。例如，我们可能检索 100，000 个用户的列表，但是需要分别请求他们的评论。

这将是一个昂贵的操作(而且无论您使用的是 REST 还是 GraphQL，都可能是糟糕的设计)。

## GraphQL 是一个规范

许多人把 GraphQL 想象成一个 ORM 或替代数据库。事实并非如此。它只是一个可以用任何语言实现的规范，描述了一种与数据相关的方式。该规范易于阅读，可在此处查看。

# 使用 GraphQL 的利弊

### 无过度蚀刻

如上所述，客户端不会收到它不会使用的数据。相反，我们只是简单地请求我们需要的东西，GraphQL 返回适当的数据。这意味着我们拥有更小的有效负载，更有效的资源利用，以及更大的前端灵活性。

### 现代

这对于一些人来说比其他人更重要，但不可否认的是，GraphQL 在开发人员中处于“较冷”的一端。这是一项很有潜力的有趣技术。

在 Javascript 2018 调查的*状态中，GraphQL 以惊人的*87.7%听说过 GraphQL 的开发者想要学习它的比例赢得了“最高兴趣”类别！**

 *### 强类型

GraphQL 使用映射 API 的强类型系统。API 中公开的所有内容都包含在使用模式定义语言的 GraphQL 模式中。一旦创建了模式，后端和前端就只有一个真实的来源，不需要在两者之间进行转换。

这样可以减少错误，在编译时进行验证，并且可以确定传递给 API 的内容和从 API 接收的内容。

### 灵活

通过允许客户端指定它需要的数据，您的前端可以更快地移动，而不会占用大量的后端资源。这明显优于端点对请求的 1:1 匹配，在 1:1 匹配中，前端需要标准化大量数据，以便向最终用户呈现正确的值。

# 图 QL 的 Cons

### 这是新的

这不一定是一件坏事，但是这个生态系统虽然在成长，但是还没有成熟到你应该选择使用标准的 REST 设置。

对许多公司来说，选择“无聊”选项更有意义。这没什么不好！对于其他人来说，GraphQL 是一种令人兴奋的新的做事方式。

### 复杂查询

这在 GraphQL 中算是一个“已解决”的问题，但它可能会让人出错。例如，如果用户(可能是恶意的)发出这样的请求会发生什么:

```
query {
  User(id: "1") {
    name
    email
    about
    address
    city
    zip
    country
    comments {
      title
      body
    }
    pictures {
      url
      order
    }
    likes {
      website {
        title
        url
      }
    }
  }
} 
```

这是一个有点琐碎的例子，但你明白了。查询会迅速膨胀并消耗大量服务器资源。在 REST API 中，您可能会调整每个端点，以最特定的方式检索其特定数据。

在 GraphQL 中，您可以使用像 [DataLoader](https://github.com/graphql/dataloader) 这样的工具，通过批处理和缓存来处理您的请求，以减少服务器上的负载。

此外，您可以通过查询成本分析来限制查询的复杂性。有几个软件包可以让这变得更容易，比如 GraphQL-Cost-Analysis ，它可以计算查询的成本，如果超过某个阈值，可以选择阻止它。

### 学习曲线

由于 GraphQL 相当新，你可能需要时间来熟悉它并弄清楚它是否适合你。此外，与 REST 相比，还有一些您可能不熟悉的“问题”。然而，有许多资源涵盖了这些。

# 我对 GraphQL 的体验

在 [BeatGig](https://beatgig.com) 我们正在使用 GraphQL 来支持我们的新 API，我的体验在很大程度上是非常积极的。尽管这需要一点学习时间，但它允许非常快速的开发以及后端和前端之间更快的集成。

这不是什么灵丹妙药，但当需要一种非常灵活的方式来查询和订阅我们的数据时，它对我们来说非常有用。在设置和理解如何将我们现有的功能映射到 GraphQL 的过程中，有一些最初的痛苦，但是到目前为止进展得相当顺利。

然而，让我们受益匪浅的是使用了 [GraphQL-Yoga](https://github.com/prisma/graphql-yoga) 和 [Prisma](https://prisma.io) ，它们为我们提供了许多现成的 CRUD 特性，并在 GraphQL-Yoga 中预先配置了一个带有 Apollo 的 express 服务器。

这使得我们可以很快地启动和运行，从那以后，开发就成了一种非常愉快的经历！

# 结论

GraphQL 并不适合所有人，但它是一项非常有趣的技术，可以减少您在使用 REST 端点时的一些挫折感(特别是在前端)。

它有一个初始的学习曲线，但允许快速开发和灵活访问您的底层数据。该生态系统发展非常迅速，大量开发人员有兴趣了解它，因此该生态系统应该会继续发展。

总的来说，它是任何开发人员宝库中的有用工具，绝对值得一试！*