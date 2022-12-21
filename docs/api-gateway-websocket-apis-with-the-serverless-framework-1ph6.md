# 具有无服务器框架的 API 网关 WebSocket APIs

> 原文：<https://dev.to/neverendingqs/api-gateway-websocket-apis-with-the-serverless-framework-1ph6>

我最近用 JavaScript 构建了一个 web 应用程序，利用 WebSockets 显示来自服务器的实时数据。我在后端使用了[AWS API Gateway web socket API](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-websocket-api.html)，在前端使用了 [WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) 。我还使用了[无服务器框架](https://serverless.com/framework/docs/providers/aws/events/websocket/)(简称“无服务器”)来部署后端。

在这篇文章中，我将快速浏览一下我写的一个简单的 WebSocket 应用程序。虽然我将分享无服务器配置的片段，但是这些示例应该足够丰富，可以将其应用到您正在使用的部署方法中。

## 后端

后端由一个 API 网关 WebSockets API、一些 Lambda 函数和一个持久性存储组成。

### 路由 WebSocket 消息

我必须做的第一件事是创建一个 WebSocket API 并配置三个预定义的路由:

```
# serverless.yml
functions:
  # Create a Lambda function that responds to messages for the predefined routes
  websocket:
    handler: src/websocket.handler
    events:
      - websocket:
          # Handles new connection requests
          route: $connect

      - websocket:
          # Route messages here if '$request.body.action' is 'routeA'.
          # You can adjust which property to use for routing by adjusting
          # 'websocketsApiRouteSelectionExpression'.
          route: routeA

      - websocket:
          # Handles all unrouted messages
          route: $default

      - websocket:
          # Handles disconnect messages
          route: $disconnect 
```

无服务器将配置 API Gateway，以便在收到消息时调用在`src/websocket.handler`定义的 Lambda 函数。该函数应该如下所示:

```
// src/websocket.js
exports.handler = async function(event, context) {
  const { requestContext: { routeKey }} = event;
  switch(routeKey) {
    case '$connect':
      ...
      break;

    case '$disconnect':
      ...
      break;

    case 'routeA':
      ...
      break;

    case '$default':
    default:
      ...
  }

  // Return a 200 status to tell API Gateway the message was processed
  // successfully.
  // Otherwise, API Gateway will return a 500 to the client.
  return { statusCode: 200 };
} 
```

我选择在同一个 Lambda 函数中处理来自任何路由的消息，但是您可以将它拆分到多个函数中。关于如何在无服务器中做到这一点的更多细节，请参见他们的 [WebSocket 文档](https://serverless.com/framework/docs/providers/aws/events/websocket/)。

### WebSocket 会话

当 WebSocket 客户端请求一个新的连接时，API Gateway 会为该会话分配一个连接 ID，并使用`event`有效负载(`event.requestContext.connectionId`)中的 ID 调用您的`$connect` Lambda 函数。您可以立即使用它在同一个函数调用中向客户机发送消息。如果您想稍后发送消息，您需要将连接 ID 存储在某个地方。

在代码示例中，我使用 DynamoDB 来存储连接 ID:

```
// src/websocket.js
case '$connect':
  await dynamodb.put({
    TableName: connectionTable,
    Item: {
      connectionId,
      // Expire the connection an hour later. This is optional, but recommended.
      // You will have to decide how often to time out and/or refresh the ttl.
      ttl: parseInt((Date.now() / 1000) + 3600)
    }
  }).promise();
  break; 
```

这允许[广播 Lambda 函数](https://github.com/neverendingqs/serverless-websocket-example/blob/master/src/broadcast.js)在广播消息时获得所有连接的 WebSocket 客户端的列表。

我还确保在客户端关闭 WebSocket 连接时清理会话:

```
// src/websocket.js
case '$disconnect':
  await dynamodb.delete({
    TableName: connectionTable,
    Key: { connectionId }
  }).promise();
  break; 
```

### 使用 ApiGatewayManagementApi 类

要将消息发送回客户端，您需要使用 [ApiGatewayManagementApi 类](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/ApiGatewayManagementApi.html)。与调用实例方法时提供资源位置的其他 AWS 服务不同(例如，`putObject()`的 S3 桶名或`putItem()`的 DynamoDB 表名)，您需要在创建类时提供 WebSocket API 的 URL:

```
const apig = new AWS.ApiGatewayManagementApi({
  endpoint: // your WebSocket API endpoint here
}); 
```

然后，您可以使用`postToConnection()`向客户端发送消息:

```
await apig.postToConnection({
  ConnectionId: connectionId,
  Data: body
}).promise(); 
```

端点遵循特定的格式，您可以使用 CloudFormation 的 join 和 reference 函数对其进行格式化:

```
# serverless.yml
# <logical ID of the WebSocket API>.execute-api.<AWS region>.amazonaws.com/<stage>
APIG_ENDPOINT:
  Fn::Join:
    - ''
    - - Ref: WebsocketsApi
      - .execute-api.
      - Ref: AWS::Region
      - .amazonaws.com/
      - ${self:custom.stage} 
```

如果您在创建新的`ApiGatewayManagementApi`对象时没有提供端点，那么当您调用`postToConnection()`时，您将得到如下错误:

```
{
    "errorMessage": "Inaccessible host: `execute-api.us-east-1.amazonaws.com'. This service may not be available in the `us-east-1' region.",
    "errorType": "Error",
    "stackTrace": [
        "UnknownEndpoint: Inaccessible host: `execute-api.us-east-1.amazonaws.com'. This service may not be available in the `us-east-1' region.",
        "    at Request.ENOTFOUND_ERROR (/serverless-websocket-example/node_modules/aws-sdk/lib/event_listeners.js:494:46)",
        "    at Request.callListeners (/serverless-websocket-example/node_modules/aws-sdk/lib/sequential_executor.js:106:20)",
        "    at Request.emit (/serverless-websocket-example/node_modules/aws-sdk/lib/sequential_executor.js:78:10)",
        "    at Request.emit (/serverless-websocket-example/node_modules/aws-sdk/lib/request.js:683:14)",
        "    at ClientRequest.error (/serverless-websocket-example/node_modules/aws-sdk/lib/event_listeners.js:333:22)",
        "    at ClientRequest.<anonymous> (/serverless-websocket-example/node_modules/aws-sdk/lib/http/node.js:96:19)",
        "    at ClientRequest.emit (events.js:189:13)",
        "    at ClientRequest.EventEmitter.emit (domain.js:441:20)",
        "    at TLSSocket.socketErrorListener (_http_client.js:392:9)",
        "    at TLSSocket.emit (events.js:189:13)",
        "    at TLSSocket.EventEmitter.emit (domain.js:441:20)",
        "    at emitErrorNT (internal/streams/destroy.js:82:8)",
        "    at emitErrorAndCloseNT (internal/streams/destroy.js:50:3)",
        "    at process._tickCallback (internal/process/next_tick.js:63:19)"
    ]
} 
```

此外，如果您没有使用`nodejs10.x`运行时，您可能会得到以下错误:

```
{
    "errorMessage": "AWS.ApiGatewayManagementApi is not a constructor",
    "errorType": "TypeError",
    "stackTrace": [
        "Module._compile (module.js:652:30)",
        "Object.Module._extensions..js (module.js:663:10)",
        "Module.load (module.js:565:32)",
        "tryModuleLoad (module.js:505:12)",
        "Function.Module._load (module.js:497:3)",
        "Module.require (module.js:596:17)",
        "require (internal/module.js:11:18)"
    ]
} 
```

AWS JavaScript SDK 在`v2.379.0`中引入了`ApiGatewayManagementApi`类，但是`nodejs8.10`运行时只有 SDK 的`v2.290.0`。如果你正在使用`nodejs8.10`，你要么必须切换到使用`nodejs10.x` ( `nodejs10.x`目前在`v2.437.0`)要么捆绑你自己版本的`aws-sdk`。参见 AWS 的 Lambda 运行时文档了解更多细节，包括其他语言运行时的信息。

### 创建授权人λ函数

授权器 Lambda 功能是可选的(但建议使用)。它在调用`$connect` Lambda 函数之前被调用，以围绕授权做出决定。例如，您可以检查授权头中的令牌，如果令牌无效，则拒绝请求。

在示例代码中，我创建了一个非常简单的 authorizer Lambda 函数，它执行一个无趣的检查:

```
// src/authorizer.js
function createAuthorizedResponse(resource) {
  return {
    principalId: 'me',
    policyDocument: {
      Version: '2012-10-17',
      Statement: [{
        Action: 'execute-api:Invoke',
        Effect: 'Allow',
        Resource: resource
      }]
    }
  };
}

exports.handler = async function(event, context) {
  // For debug purposes only.
  // You should not log any sensitive information in production.
  console.log("EVENT: \n" + JSON.stringify(event, null, 2));

  const { headers, methodArn } = event;

  // This is for demo purposes only.
  // This check is probably not valuable in production.
  if(headers['X-Forwarded-Proto'] === 'https') {
    return createAuthorizedResponse(methodArn);
  } else {
    throw new Error('Unauthorized');
  }
} 
```

对于 REST APIs，您可以为任何路由配置一个授权器 Lambda 函数。对于 WebSocket APIs，只有`$connect`路由可以有一个与之相关联的授权函数。如果您尝试使用授权功能配置其他路线，您会得到如下错误:

```
An error occurred: routeAWebsocketsRoute - Currently, authorization is restricted to the $connect route only (Service: AmazonApiGatewayV2; Status Code: 400; Error Code: BadRequestException; Request ID: a04ea057-954f-436f-962e-3946639f3d15). 
```

这意味着仅在初始连接时检查授权，并且连接 ID 是发送到其他路由的消息的唯一授权检查。您应该像保护会话 ID 一样保护它。

如果您的应用程序有一个现有的会话管理系统，该系统还可以做出授权决策，那么它必须将当前会话与 API Gateway 分配的连接 ID 相关联。讨论如何做到这一点超出了本文的范围，但是您可能想看看如何[将上下文从授权者 Lambda 函数](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-lambda-authorizer-output.html)传递给处理`$connect`消息的 Lambda 函数。

如果授权者 Lambda 函数拒绝连接请求，WebSocket 客户端将收到一个 401。

## 前端

[前端代码](https://github.com/neverendingqs/serverless-websocket-example/tree/master/client)以`WebSocket`类为中心:

```
// The endpoint's protocol is either 'ws://' or 'wss://'.
// The preferred protocol is 'wss://'.
const websocket = new WebSocket(endpoint); 
```

有四个相关的事件/事件处理程序:

*   `close` / `onclose`
*   `error` / `onerror`
*   `message` / `onmessage`
*   `open` / `onopen`

要监听这些事件，请为其创建一个相应的函数:

```
websocket.onmessage = ({ data }) => {
  // handle the message
}; 
```

`onmessage`是最重要的一个，因为它是处理来自服务器的新消息的地方，而`onopen`、`onerror`和`onclose`对于管理应用程序的状态很有用。`onclose`也很有用，可以让你知道 API Gateway 什么时候关闭连接，因为它是在不活动时关闭的。

每个事件都有不同的可用属性，详细信息可在 [HTML 生活标准](https://html.spec.whatwg.org/multipage/indices.html#events-2)页面上找到。

顺便提一下，`WebSocket`在页面重新加载或关闭时向服务器发送一条`$disconnect`消息，因此在这些情况下您不必自己关闭连接。

## 结论

在这篇文章中，我快速回顾了我在构建一个简单的`WebSocket`应用程序时所学到的东西。请随意使用任何[工作样本代码](https://github.com/neverendingqs/serverless-websocket-example)，并请让我知道下面的任何问题或意见！