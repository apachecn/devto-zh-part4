# 在 WIP API 中使用 GraphQL

> 原文：<https://dev.to/evaneykelen/using-graphql-with-the-wip-api-2cb1>

WIP([https://wip.chat/](https://wip.chat/))是由[马克·科尔布鲁日](https://wip.chat/@marckohlbrugge/)创办的创客社区。最近，我深入研究了它的 GraphQL API，以了解其功能。下面你会找到我的笔记。

注册后，你可以进入[这个页面](https://wip.chat/api)显示你的私有 API 密钥。

让我们从请求您自己的帐户资料开始。

必须设置两个标题:

*   `Authorization: bearer YOUR-PRIVATE-API-KEY`
*   `Content-Type: application/json`

API 端点是[https://wip.chat/graphql](https://wip.chat/graphql)。

发送以下查询以获取您的帐户配置文件数据:

```
{
  viewer {
    id
    url
    username
    first_name
    last_name
    avatar_url
    best_streak
    completed_todos_count
    products {
      name
      todos {
        body
      }
    }
    todos {
      body
    }
    streaking
  }
} 
```

注意`viewer`的用法。在 GraphQL APIs 中经常会看到这种情况。它指的是经过身份验证的用户。它不是一个真正的 GraphQL 规范，而是起源于脸书的[实践](https://medium.com/the-graphqlhub/graphql-and-authentication-b73aed34bbeb)(就像 GraphQL 一样)。

WIP API 使用`viewer`返回与经过认证和授权的 API 用户相关的数据。

结果看起来是这样的:

```
{
  "data": {
    "viewer": {
      "id": "1377",
      "url": "https://wip.chat/@realhackteck",
      "username": "realhackteck",
      "first_name": "Erik",
      "last_name": "van Eykelen",
      "avatar_url": "https://wip.imgix.net/store/user/1377/avatar/5c9fae4c0cd2343c8a86edbf822825b4.jpg?ixlib=rb-1.2.2&w=64&h=64&fit=crop&s=6c4e9eb0dd3b1679c34b056ad532d05b",
      "best_streak": 0,
      "completed_todos_count": 0,
      "products": [
        {
          "name": "Wip.Chat API test",
          "todos": []
        }
      ],
      "todos": [],
      "streaking": false
    }
  }
} 
```

注意:用`user(username: "realhackteck")`替换`viewer`将返回相同的数据。

GraphQL 很酷的一点是它提供自省的能力:

[![Insomnia](img/c21d67960bee56e93c40c6df32882c96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zhEc-hbu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/11846/59552940-b321ac80-8f8d-11e9-9373-fe70b70bc609.png)

截图显示了我经常用来运行 API 测试的[失眠症](https://insomnia.rest/)客户端。带有橙色 Gs 的弹出菜单显示属于`Todo`对象的属性。失眠症患者能够通过查询 GraphQL 模式来显示这些属性。[这篇文章](https://graphql.org/learn/introspection/)解释了内省是如何工作的。

下一步是使用 API 创建一些东西。以下示例显示了如何创建包含附件的 WIP todo。要做到这一点，需要三个步骤。

## 第一步

生成可用于上传附件的预签名 URL:

```
mutation createPresignedUrl {
  createPresignedUrl(input: {filename: "foobar.jpg"}) {
    fields
    headers
    method
    url
  }
} 
```

结果看起来是这样的:

```
{
  "data": {
    "createPresignedUrl": {
      "fields": "{\"key\":\"cache/c79b...7fe0.jpg\",\"Content-Type\":\"image/jpeg\",\"policy\":\"eyJl...fQ==\",\"x-amz-credential\":\"AKIA...HQKQ/20190615/us-east-1/s3/aws4_request\",\"x-amz-algorithm\":\"AWS4-HMAC-SHA256\",\"x-amz-date\":\"20190615T150217Z\",\"x-amz-signature\":\"497b...13df\"}",
      "headers": "{}",
      "method": "post",
      "url": "https://s3.amazonaws.com/assets.wip.chat"
    }
  }
} 
```

## 第二步

使用之前的结果将附件上传到 AWS S3:

```
curl -F "key=cache/c79b...7fe0.jpg" \
     -F "Content-Type=image/jpeg" \
     -F "Policy=eyJl...fQ==" \
     -F "x-amz-credential=AKIA...HQKQ/20190601/us-east-1/s3/aws4_request" \
     -F "x-amz-algorithm=AWS4-HMAC-SHA256" \
     -F "x-amz-date=20190615T150217Z" \
     -F "x-amz-signature=497b...13df" \
     -F "file=@foobar.jpg" \
     https://s3.amazonaws.com/assets.wip.chat 
```

## 第三步

向 GraphQL API 发送一个[突变](https://graphql.org/learn/queries/)请求:

```
mutation createTodo {
  createTodo(input: {body: "This todo has an attachment", attachments: [{key: "cache/c79b...7fe0.jpg", filename: "foobar.jpg", size: 1001}]}) {
    id
    body
    user {
      first_name
      last_name
    }
  }
} 
```

结果看起来是这样的:

```
{
  "data": {
    "createTodo": {
      "id": "117462",
      "body": "This todo has an attachment",
      "user": {
        "first_name": "Erik",
        "last_name": "van Eykelen"
      }
    }
  }
} 
```

如果你有兴趣知道你能用 WIP API 做什么，请访问 https://wip.chat/graphiql。该页面运行 GraphiQL，这是一个图形化的浏览器内 IDE。查看 https://github.com/graphql/graphiql 的[了解更多信息。](https://github.com/graphql/graphiql)

本文最初发布于[https://www . msgtrail . com/articles/2019 06 15-1735-using-graph QL-with-the-WIP-API/](https://www.msgtrail.com/articles/20190615-1735-using-graphql-with-the-wip-api/)