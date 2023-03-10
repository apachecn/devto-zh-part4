# AWS Lambda 上的无服务器节点应用

> 原文：<https://dev.to/victorhazbun/serverless-node-apps-on-aws-lambda-2m0a>

## 什么是无服务器？

作为服务的功能(FaaS)。这些由临时容器组成，它们可以自动伸缩，并有按执行付费的定价。

还在迷茫？

它在云中运行你的功能，不需要一个服务器周期。

## 优点

*   低价位，执行这些功能的成本比拥有自己的定制主机要低得多。
*   **自动扩展**，您不需要担心服务器的扩展，因为您并没有真正的服务器。随着函数接收越来越多的请求，AWS Lambda 会自动为您完成。
*   **零可维护性**，你不需要维护托管你功能的服务器。

## 弊

*   **冷热型**，功能关闭，然后在请求发出后再打开，这将导致响应延迟。
*   **环境受阻**，你无法安装额外的软件包或软件，所以如果你的功能依赖于第三方软件包，你可以忘记使用 AWS Lambda。
*   **不同的环境**，你在一个实例中所做的更改，并不能保证在下一个实例中持久化。所有将被消灭(随机)。

## 如何对抗骗局

### 冷热搭配

您可以使用[预热](https://serverless.com/blog/keep-your-lambdas-warm/)

### 环境受阻

你可以告诉你的函数使用一个 API 来承载你所依赖的软件。

### 不同的环境

您可以使用 AWS S3 存储您需要持久化的文件

## 设置

访问 AWS AIM 控制台，创建一个新用户，然后授予他编程访问权限，最后授予他`AdministratorAccess`。一旦确认，存储**访问密钥 ID** 和**秘密访问密钥**。

最后安装 AWS CLI，设置访问密钥**访问密钥 ID** 和**秘密访问密钥**。

`brew install awscli`

`aws configure`

## 无服务器框架

安装无服务器框架。

`npm install -g serverless`

## 手动设置

让我们在应用程序中手动设置无服务器框架。并公开两个 API 端点，`create user`和`get one user`。

`serverless create --template aws-nodejs --path api`

`cd api`

`mkdir todos`

```
//  api/package.json  {  "name":  "api-todos",  "version":  "1.0.0",  "description":  "Create and Get one Todo",  "author":  "",  "license":  "MIT",  "dependencies":  {  "uuid":  "^2.0.3"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

安装我们的依赖项`npm install` (AWS Lambda 也会安装依赖项)。

```
# api/serverless.yml
service: api

provider:
  name: aws
  runtime: nodejs8.10
  environment:
    DYNAMODB_TABLE: ${self:service}-${opt:stage, self:provider.stage}
  iamRoleStatements:
    - Effect: Allow
      Action:
        - dynamodb:GetItem
        - dynamodb:PutItem
      Resource: "arn:aws:dynamodb:${opt:region,  self:provider.region}:*:table/${self:provider.environment.DYNAMODB_TABLE}"

functions:
  create:
    handler: todos/create.create
    events:
      - http:
          path: todos
          method: post
          cors: true
  get:
    handler: todos/get.get
    events:
      - http:
          path: todos/{id}
          method: get
          cors: true

resources:
  Resources:
    TodosDynamoDbTable:
      Type: 'AWS::DynamoDB::Table'
      DeletionPolicy: Retain
      Properties:
        AttributeDefinitions:
          -
            AttributeName: id
            AttributeType: S
        KeySchema:
          -
            AttributeName: id
            KeyType: Hash
        ProvisionedThroughput: 
          ReadCapacityUnits: 1
          WriteCapacityUnits: 1
        TableName: ${self:provider.environment.DYNAMODB_TABLE} 
```

Enter fullscreen mode Exit fullscreen mode

接下来创建每个函数文件。

```
// api/todos/create.js
'use strict';

const AWS = require("aws-sdk");
const uuid = require ("uuid/v4");
const client = new AWS.DynamoDB.documentClient();

module.exports.create = async (event) => {
  const data =JSON.parse(event.body);
  const params = {
    TableName: "todos"' Item: {
      id: uuid(),
      text: data.text,
      checked: false
    }
  };

  await client.put(params).promise();
  return{
    statusCode: 200,
    body: JSON.stringify(data)
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
// api/todos/get.js

'use strict';
const AWS = require("aws-sdk");

const dynamoDb = new AWS.DynamoDB.DocumentClient();

module.exports.get = async (event) => {
  const params = {
    TableName: "todos",
    Key: {
      id: event.pathParameters.id
    }
  };

  const result = await dynamoDb.get(params).promise();
  if (result.Item) {
    return {
      statusCode:200,
      body: JSON.stringify(result.Item)
    };
  } else {
    return {
      statusCode: 404,
      body: JSON.stringify({ message: "Couldn't find the todo item." })
    };
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `event`是包含请求数据的对象。
*   `context`是包含 AWS 信息的对象。
*   `callback`是一个函数，将调用错误响应作为第一个参数，或将有效响应作为第二个参数。

## 部署

部署是最酷的部分之一。

`sls deploy`

部署后，您可以测试该功能。

### 创建待办事宜

`curl -X POST https://XXXXXXX.execute-api.us-east-1.amazonaws.com/dev/todos --data '{ "text": "Learn Serverless" }'` 
输出:

`{"text":"Learn Serverless","id":"ee6490d0-aa11e6-9ede-afdfa051af86","createdAt":1479138570824,"checked":false,"updatedAt":1479138570824}%` 

### 搞定一件待办事宜

`# Replace the <id> part with a real id from your todos table
curl https://XXXXXXX.execute-api.us-east-1.amazonaws.com/dev/todos/<id>` 

输出:

`{"text":"Learn Serverless","id":"ee6490d0-aa11e6-9ede-afdfa051af86","createdAt":1479138570824,"checked":false,"updatedAt":1479138570824}%` 

### 更多关于部署

您也可以只部署该功能(这相当快)。

`serverless deploy function -f create`

## DynamoDB

创建表时，可以指定要为读取和写入保留多少调配的吞吐量容量。DynamoDB 将保留必要的资源来满足您的吞吐量需求，同时确保一致的低延迟性能。您可以更改调配的吞吐量，并根据需要增加或减少容量。

## 并发执行

默认情况下，AWS Lambda 将给定区域内所有函数的总并发执行数限制为 100。默认限制是一个安全限制，保护您在初始开发和测试期间免受潜在失控或递归函数的成本影响。要将这个限制增加到默认值以上，请按照[中的步骤请求增加并发执行的限制](http://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html#increase-concurrent-executions-limit)。

## 处理大量交通

如果你预计会有很多流量，建议切换到 DynamoDB 的自动缩放选项[。](https://aws.amazon.com/blogs/aws/auto-scale-dynamodb-with-dynamic-dynamodb/)

## 真实世界用例

AWS Lambda 或任何其他 Lambda 可用于多种目的，例如:

*   数据处理
*   后端
*   物联网
*   蝇蛆病

[了解更多关于用例的信息](https://serverless.com/learn/use-cases/)

## 最后的想法

不再有服务器了？当然不是。这不会取代发球(至少现在不会)。然而，它是开发微服务和许多其他事情的一个很好的工具。