# 💡使用 RESTful 重新发明 GraphQL——在 4 分钟内从 RESTful 的角度学习 graph QL。

> 原文：<https://dev.to/getd/reinvent-graphql-using-restful-learn-graphql-from-the-perspective-of-restful-in-4-mins-3khk>

对于初学者来说，GraphQL 表面上看起来很复杂，但实际上并不复杂。这篇文章展示了如何通过在 RESTful API 中逐步发明 GraphQL 的核心概念来学习它们。

让我们回到 2014 年。GraphQL 还不是一个东西，我们的 AWESOME-REST-API 服务器刚刚上线了一个简单的 RESTful API，它通过`id` :
返回用户`name`

```
// A REST API to get user name by user id
GET /user/:id/name
// e.g., get user 123's name
GET /user/123/name
> techbos 
```

有一天我们决定也归还用户的`age`。所以我们有 4 个选择:

*   选项 A:为`age`创建一个新端点

```
GET /user/123/age
> 23 
```

*   选项 B:将`age`与`name`合并

```
GET /user/123/nameAndAge
> { "name": "techbos", "age": 23 } 
```

*   选项 C:返回整个`user`对象，让客户选择他们想要的字段

```
GET /user/123
> { "name": "techbos", "age": 23, "id": 123, "location": "Seattle", ... } 
```

*   选项 D:使用查询参数

```
GET /user/123?fields=name,age
> { "name": "techbos", "age": 23 } 
```

很容易看出选项 A、B、C 有其问题:

*   选项 A:获取`name`和`age`需要客户端发出两个 http 请求。
*   选项 B:随着时间的推移，我们可能会有数百个“组合”端点。
*   选项 C:大量浪费带宽。

选项 D 似乎是合理的。所以我们决定试一试，并在过程中不断改进:

# 使用类似 JSON 的请求体

首先，`?fields=name`对于简单的字段工作良好，但是当我们有对象/嵌套字段时就变得复杂了，例如下面的`name`对象的`first`和`last`:

```
{
  "name": {
    "first": "tech",
    "last": "bos",
  }
} 
```

为了解决这个问题，我们用一个类似 JSON 的请求体替换 url 查询参数`?fields=`。我们也用`POST`代替`GET`。

```
// Replace ?fields=name,age with request body
POST /user/123
// JSON-like request body for selecting fields
{
  name {
    first
  }
  age
}
// response
> {
    "name": {
      "first": "tech"
    },
    "age": 23
  } 
```

这给了我们一种清晰的方式来告诉服务器返回哪些字段。干净利落。

# 批量多个请求

接下来，我们注意到我们的客户端代码总是使用两个独立的请求同时获取`user`和`posts`:

```
// Get user 123's first name and age
POST /user/123 { name { first }, age }
// Get user 123's post title and content
POST /posts/user/123 { title, content } 
```

我们可以只提一个要求吗？当然可以。我们可以将它们合并成一个单独的请求体，并使用一个单独的端点`/smartql`，它代表“智能查询语言”:

```
POST /smartql
// One request body to query for two things
{
  // Replaces /user/123
  user(id: 123) {
    name {
      first
    }
    age
  }
  // Replaces /posts/user/123
  posts(userId: 123) {
    title
    content
  }
}
// response
> {
    "user": {
      "name": {
        "first": "tech"
      },
      "age": 23,
    },
    "notifications": [{
      "title": "Notification 1",
      "content": "Super important",
    }, {
      "title": "Notification 2",
      "content": "Not very important",
    }],
  } 
```

现在，我们可以在一个请求中批量获取多个端点。干净利落。

# 使 API 强类型化

随着我们 API 的增长，前端和后端工程师同步 API 变化变得越来越困难。我们的 API 文档似乎总是过时，任何 API 更改都很容易导致 bug。

例如，我们将`age`字段更改为 null-able，这导致了许多客户端崩溃，因为代码假设`age`总是有效的。要解决这个问题:

*   首先，在服务器端，我们创建一个数据`Schema`并使用它来确保进出我们服务器的任何数据都必须遵循预期的格式。例如，我们可以定义一个`User`或`Post`包含什么字段，并使用一个根`Query`类型来定义客户端如何使用`userId`等参数查询数据。

```
# Data type for a user  type  User  {  name:  Name!  # non-null!  age:  Int  # nullable  }  # Data type for a user name  type  Name  {  first:  String  last:  String  }  # Data type for a notification  type  Post  {  title:  String!  content:  String  }  # Root query type for request body  type  Query  {  user(id:  Int!):  User  posts(userId:  Int!):  [Post]  # an array of Posts  } 
```

*   接下来，在客户端，我们将所有的类型模式从服务器下载到一个`schema.smartql`文件中，并使用静态代码检查工具如`TypeScript`或`Flow`来确保客户端代码遵循模式。例如，如果我们访问用户的`age`而不进行空检查，或者如果我们查询一个字符串类型为`id`的`user`对象，我们将从类型检查中得到一个错误。

有了这个解决方案，我们再也不需要维护 API 文档，因为 API 本身变成了一个活的文档。

我们都为这个新的`SmartQL`感到非常高兴。一年后，我们决定用`GraphQL` *的名字来开源它，因为为什么不用*。这就是 GraphQL 的起源...

开玩笑🙃

我希望这篇文章能帮助你理解 GraphQL 的关键概念。您可以在[中使用上面的 GraphQL 查询示例🏀getd.io 的游乐场🏀](https://getd.io/workspace?r=%7B%22requestMode%22%3A%22CLOUD%22%2C%22method%22%3A%22POST%22%2C%22url%22%3A%22https%3A%2F%2Fgetd.io%2Fgraphql%22%2C%22params%22%3A%5B%5D%2C%22authorization%22%3A%7B%22type%22%3A%22NONE%22%7D%2C%22headers%22%3A%5B%7B%22isEnabled%22%3Atrue%2C%22key%22%3A%22content-type%22%2C%22value%22%3A%22application%2Fjson%22%7D%5D%2C%22body%22%3A%22%7B%5Cn%20%20%5C%22operationName%5C%22%3Anull%2C%5Cn%20%20%5C%22variables%5C%22%3A%7B%7D%2C%5Cn%20%20%5C%22query%5C%22%3A%5C%22%7B%5C%5Cn%20%20demoUser(id%3A%20123)%20%7B%5C%5Cn%20%20%20%20name%20%7B%5C%5Cn%20%20%20%20%20%20first%5C%5Cn%20%20%20%20%20%20last%5C%5Cn%20%20%20%20%7D%5C%5Cn%20%20%20%20age%5C%5Cn%20%20%20%20posts%20%7B%5C%5Cn%20%20%20%20%20%20title%5C%5Cn%20%20%20%20%20%20content%5C%5Cn%20%20%20%20%7D%5C%5Cn%20%20%7D%5C%5Cn%7D%5C%5Cn%5C%22%5Cn%7D%22%2C%22formData%22%3A%5B%5D%2C%22formUrl%22%3A%5B%5D%2C%22bodyEditorFormat%22%3A%22json%22%2C%22requestBuilderTab%22%3A%22body%22%7D)T3】

如果你喜欢这篇文章，请点击❤️并在 twitter 上关注我。