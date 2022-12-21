# 缝合 GraphQL 模式的更好方法

> 原文：<https://dev.to/graphqleditor/a-better-way-to-stitch-graphql-schemas-3mdn>

[模式](https://blog.graphqleditor.com/graphql-schemas-and-types-part-1/)是任何 [GraphQL](https://graphql.org/learn/) 实现中最重要的部分。Schema 是任何 GraphQL 服务的核心，它定义了客户机可以查询的数据集。

#### 什么是模式拼接？

想象一下，在一个项目中，每个团队都有多个团队参与:

*   处理对该服务至关重要的不同方面，
*   必须处理模式，以确保他们的项目部分能够按预期工作，
*   我必须确保所有的东西都能正常运转。

在大型项目模式上合作可能会令人困惑...

[![What's going on in this schema](img/2fafd524261f0c301be3534614f93835.png)](https://graphqleditor.com/)

###### 一块 [GitHub 的 schema](https://developer.github.com/v4/) 共包含 9182 行代码！

模式拼接是将多个 GraphQL 模式合并成一个可以查询的模式的过程。当构建需要对跨多个 API 的数据进行统一和透明的访问的项目时，这是一种非常有用的方法(即使不知道它的确切位置)。它还可以用于定制现有的 GraphQL API，如添加定制解析器或覆盖现有的第三方 [GraphQL 服务](https://graphqleditor.com/)或向数据来自另一个数据源的现有类型添加更多字段。

[![Two schemas merged](img/1d318b0f5122edb50a5d27bb499b4bcc.png)](https://graphqleditor.com/)

#### 执行模式拼接会带来麻烦

虽然以一种没有名称冲突的方式将两个模式合并成一个模式看起来很简单，但这并不是真正的模式拼接。我们想要实现的是一个一致的模式，由多个底层 GraphQL APIs 合并而成。这是一个更困难的方法，但如果 a 告诉你有一个应用程序可以做到这一点呢？

#### 更好的方法

GraphQL 编辑器的最新版本引入了一种简单的模式协作方式。我们需要做的就是:

**1。向我们的帐户添加两个或更多模式-您可以从 URL 或。gql 文件**

[![Add some schemas to GraphQL Editor](img/8073d37a11825717e6a790f494a99dd1.png)](https://app.graphqleditor.com/?category=examples&visibleMenu=projects)

**2。立项**
[![GraphQL Editor](img/db043325f8588db572ec5149d9d5abb1.png)](https://app.graphqleditor.com/?category=examples&visibleMenu=projects) 
**3。从列表中选择我们想要访问的新生成的模式&创建一个项目**
[![Stitching Two Schemas in GraphQL Editor](img/b1a3a460487d9fbcdf4bdabd5dfef3a2.png)](https://app.graphqleditor.com/?category=examples&visibleMenu=projects)

...**就这样！**

只要我们设法避免了底层模式之间的冲突，我们就应该能够访问新生成的项目中的所有元素👌

[![Now you have access to all elements of merged schema](img/4340d33aa87a6b8b2f4004edd6f74cc7.png)](https://graphqleditor.com/)