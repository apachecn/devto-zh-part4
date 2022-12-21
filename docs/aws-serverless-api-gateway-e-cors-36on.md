# AWS 无服务器- API 网关 e CORS

> 原文：<https://dev.to/oieduardorabelo/aws-serverless-api-gateway-e-cors-36on>

## 您的无服务器 API 生存指南

创建 web API 是无服务器应用程序最常用的示例之一。您可以获得简单、可扩展的后端带来的好处，而不会增加操作开销。

但是，如果您有一个正在调用 API 的网页，则需要处理**跨原点资源共享**或**颜色**。如果您的网页向当前域以外的域发出 HTTP 请求，则它必须与颜色兼容。

如果您以前遇到过以下错误:

> 请求的资源上没有“Access-Control-Allow-Origin”标头

那你来对地方了！

本文将讨论您需要了解的关于**server less+colors**的所有信息。如果你不介意细节，见第 **TL 节；**博士在下面。否则，我们将处理:

*   印前检查请求(“T0”印前检查请求“”
*   回应标头(*回应标头*
*   具有自定义授权的颜色
*   CORS 网站 Cookie 凭证( *Cookie 凭证*

我们开始吧！

* * *

# TL；速度三角形定位法(dead reckoning)

如果要在 serverless 应用程序中快速、肮脏地解决颜色问题，请执行以下操作:

1.  要处理**印前检查请求**，请在您的`serverless.yml`中的每个 HTTP 端点添加`cors: true`:

```
# serverless.yml

service: products-service

provider:
  name: aws
  runtime: nodejs6.10

functions:
  getProduct:
    handler: handler.getProduct
    events:
      - http:
          path: product/{id}
          method: get
          cors: true # <-- CORS!
  createProduct:
    handler: handler.createProduct
    events:
      - http:
          path: product
          method: post
          cors: true # <-- CORS! 
```

1.  为了处理**颜色应答标头**，您需要在应答中返回颜色应答标头。主要标题为`Access-Control-Allow-Origin`和`Access-Control-Allow-Credentials`。

您可以使用下面的示例或查看文章中讨论的中间件库来帮助您完成此操作:

```
'use strict';

module.exports.getProduct = (event, context, callback) => {

  // Busca um produto
  const product = retrieveProduct(event);

  const response = {
    statusCode: 200,
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Credentials': true,
    },
    body: JSON.stringify({
      product: product
    }),
  };

  callback(null, response);
};

module.exports.createProduct = (event, context, callback) => {

  // Cria um novo produto
  const product = createProduct(event);

  const response = {
    statusCode: 200,
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Credentials': true,
    },
    body: JSON.stringify({
      product: product
    }),
  };

  callback(null, response);
}; 
```

1.  如果您使用的是自定义授权程序，则需要在您的“`serverless.yml`”的“`serverless.yml`块中添加以下“**云形成”**:

```
# serverless.yml

...

        resources:
          Resources:
            GatewayResponseDefault4XX:
              Type: 'AWS::ApiGateway::GatewayResponse'
              Properties:
                ResponseParameters:
           gatewayresponse.header.Access-Control-Allow-Origin: "'*'"
           gatewayresponse.header.Access-Control-Allow-Headers: "'*'"
                ResponseType: DEFAULT_4XX
                RestApiId:
                  Ref: 'ApiGatewayRestApi' 
```

* * *

# 验证请求(*印前检查请求*

如果不进行“简单请求”，浏览器将使用`OPTIONS`方法发送**【印前检查请求】**。您请求的功能将返回可以发送到 API 的安全方法，并且可以根据需要返回要发送的有效标题。

让我们结束这一切。

# 当我的浏览器发送*请求*时？

您的浏览器将在几乎所有交叉来源请求中发送一个“**”印前检查请求“**”。例外是“简单请求”，但它们是相当有限的子集。

基本上，“简单请求”只是一个具有表单数据**的**请求或`POST`请求，没有认证。如果您不使用此格式，浏览器将发送“*印前检查请求“*”。

如果使用订单`PUT`或`DELETE`，则会发送订单*。如果在申请外使用`Content-Type`、`application/x-www-form-urlencoded`、`multipart/form-data`或`text/plain`的标头，则会发出*的预检请求。如果包括一些非常基本的标题以外的任何标题，例如验证标题(“T6”)，它将发送一个“*”印前检查请求“*”。**

 **# *对*请求的答复是什么？

对**预检请求**的答复包括允许访问资源的域及其方法，如`GET`、`POST`、`PUT`等。答复还可包括通信中允许的标题，如`Authentication`。

# 我如何处理与服务器无关的印前检查请求？

要配置对**请求**的响应，您需要在 **API Gateway** 中设置一个处理程序来处理`OPTIONS`方法。幸运的是，这对于 [**【无服务器框架】**](https://github.com/serverless/serverless) 来说非常简单。

只需在你的`serverless.yml` :
中的每一个*端点*加上`cors: true`

```
# serverless.yml

service: products-service

provider:
  name: aws
  runtime: nodejs6.10

functions:
  getProduct:
    handler: handler.getProduct
    events:
      - http:
          path: product/{id}
          method: get
          cors: true # <-- CORS!
  createProduct:
    handler: handler.createProduct
    events:
      - http:
          path: product
          method: post
          cors: true # <-- CORS! 
```

这将配置网关 API 以允许任何域访问，并包括基本的允许标头集。如果您需要其他自定义(高级使用)，则显示如下:

```
# serverless.yml

service: products-service

provider:
  name: aws
  runtime: nodejs6.10

functions:
  getProduct:
    handler: handler.getProduct
    events:
      - http:
          path: product/{id}
          method: get
          cors:
            origin: '*' # <-- Origens permitidas
            headers: # <-- Cabeçalhos customizados
              - Content-Type
              - X-Amz-Date
              - Authorization
              - X-Api-Key
              - X-Amz-Security-Token
              - X-Amz-User-Agent
            allowCredentials: false 
```

# 颜色应答标头

虽然*预检请求*仅适用于某些*交叉产地*请求，但颜色应答标头必须存在于所有交叉产地请求中这意味着您必须在处理程序的响应中添加标题“`Access-Control-Allow-Origin`”。

如果您使用 cookies 或其他身份验证类型，则还需要在您的响应中添加标题“`Access-Control-Allow-Credentials`”。

使用上节中的`serverless.yml`，您的文件`handler.js`应如下所示:

```
// handler.js

'use strict';

module.exports.getProduct = (event, context, callback) => {

  // Busca um produto
  const product = retrieveProduct(event);

  const response = {
    statusCode: 200,
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Credentials': true,
    },
    body: JSON.stringify({
      product: product
    }),
  };

  callback(null, response);
};

module.exports.createProduct = (event, context, callback) => {

  // Cria um novo produto
  const product = createProduct(event);

  const response = {
    statusCode: 200,
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Credentials': true,
    },
    body: JSON.stringify({
      product: product
    }),
  };

  callback(null, response);
}; 
```

请注意对象“`response`”具有属性“`headers`”，该属性包含具有“`Access-Control-Allow-Origin`”和“`Access-Control-Allow-Credentials`”的对象。

在所有函数中添加这些标题可能会很困难，尤其是如果逻辑中有多个路径。幸运的是，有一些很好的工具可以帮助我们做到这一点！

如果使用 JavaScript，请查看用于 Lambdas 的*[【middy】](https://middy.js.org)的力学。它提供了各种用途宜人的*中型软件，处理了 Lambda 函数的枯燥陈词滥调。一个是“`cors`中间件”，它会自动将彩色标题添加到其功能中。**

 **一个基本例子如下:

```
// handler.js

const middy = require('middy')
const { cors } = require('middy/middlewares')

// Esse é o seu manipulador comum, não é diferente do que você está acostumado a fazer todos os dias
// no AWS Lambda
const hello = (event, context, callback) => {

  const response = {
    statusCode: 200,
    body: "Hello, world!"
  }

  return callback(null, response)
}

// Vamos "middyfy" nosso manipulador, então poderemos conectar middlewares a ele
const handler = middy(hello)
  .use(cors()) // Adiciona cabeçalhos CORS às respostas

module.exports = { handler } 
```

完美-自动颜色标题！查看整个 Middy 库，了解许多其他方法。

如果你是个马拉松选手，[丹尼尔·谢普](https://twitter.com/schep_)制作了一个名为[【lambda-decorators】](https://github.com/dschep/lambda-decorators)的好图书馆，其目的与 Middy 相同——取代 lambda 函数的陈词滥调。

下面是它的 Python 功能的一个例子:

```
# handler.py 
from lambda_decorators import cors_headers

@cors_headers
def hello(event, context):
    return {
        'statusCode': 200,
        'body': "Hello, world!"
    } 
```

注:Daniel 是软件包的创建者[【server less-python-requirements】](https://github.com/UnitedIncome/serverless-python-requirements)，如果要用 python 编写 Lambda 函数，则必须绝对使用该软件包。查看我们以前在[python 包博客](https://serverless.com/blog/serverless-python-packaging/)上的[帖子](https://serverless.com/blog/serverless-python-packaging/)。

# 具有自定义授权的颜色

[自定义授权者](https://serverless.com/framework/docs/providers/aws/events/apigateway/#http-endpoints-with-custom-authorizers)允许您使用负责处理授权的角色来保护您的舔端点。

如果授权成功，它会将请求转发给 Lambda 处理程序。如果失败，将拒绝请求并返回给用户。

颜色问题在第二种情况下-如果您拒绝授权请求，则无法在回应中指定颜色标题。这可能会使客户的浏览器难以理解答案。

要处理此问题，您需要在您的“**API 网关”**配置中添加自定义的“**网关响应”**。你会在你的`serverless.yml` :
的`resources`块中加上这个

```
functions:
   ...

resources:
  Resources:
    GatewayResponseDefault4XX:
      Type: 'AWS::ApiGateway::GatewayResponse'
      Properties:
        ResponseParameters:
          gatewayresponse.header.Access-Control-Allow-Origin: "'*'"
          gatewayresponse.header.Access-Control-Allow-Headers: "'*'"
        ResponseType: DEFAULT_4XX
        RestApiId:
          Ref: 'ApiGatewayRestApi' 
```

这将确保在拒绝授权请求后立即从自定义授权者返回相应的响应标头。

# 带有 Cookie 凭据的颜色

> 注:本节是在 2018 年 1 月 29 日根据“[Alex rude nko](https://twitter.com/orKoN)的订单添加的。martin splitt 的最佳秘诀，他有一篇关于这个问题的文章。

在上面的示例中，我们提供了一个通配符“`"*"`”作为标题值“`Access-Control-Allow-Origin`”。但是，如果使用凭据进行[申请，则不允许使用通配符值。要使浏览器使用响应，响应标题“`Access-Control-Allow-Origin`”必须包含发出请求的特定源。](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#Requests_with_credentials)

处理这个问题有两种方法。第一，如果您只有一个来源站点正在进行请求，只需在 Lambda 的功能响应中对其进行编码即可:

```
// handler.js

'use strict';

module.exports.getProduct = (event, context, callback) => {

  // Busca um produto
  const product = retrieveProduct(event);

  const response = {
    statusCode: 200,
    headers: {
      'Access-Control-Allow-Origin': 'https://minha-origem.com.br', // <-- Adicione sua origem aqui
      'Access-Control-Allow-Credentials': true,
    },
    body: JSON.stringify({
      product: product
    }),
  };

  callback(null, response);
}; 
```

如果您有多个可能正在使用 API 的源地址，则需要采取更动态的方法。你可以检查〖t0〗题头，看看它是否在你的核定来源清单上。如果有的话，请将来源值传回标头`Access-Control-Allow-Origin` :

```
// handler.js

'use strict';

const ALLOWED_ORIGINS = [
    'https://primeira-origem.com',
    'https://segunda-origem.com',
];

module.exports.getProduct = (event, context, callback) => {

  const origin = event.headers.origin;
  let headers;

  if (ALLOWED_ORIGINS.includes(origin) {
    headers: {
      'Access-Control-Allow-Origin': origin,
      'Access-Control-Allow-Credentials': true,
    },
  } else {
      headers: {
      'Access-Control-Allow-Origin': '*',
    },
  }

  // Busca um produto
  const product = retrieveProduct(event);

  const response = {
    statusCode: 200,
    headers
    body: JSON.stringify({
      product: product
    }),
  };

  callback(null, response);
}; 
```

在此示例中，我们验证了标题“`origin`”是否与我们允许的域之一匹配。如果是真的，我们把产地列入了自己的标题`Access-Control-Allow-Origin`，还说`Access-Control-Allow-Credentials`是允许的。如果“`origin`”与权限列表中的任何项都不匹配，则我们包括默认标题，如果此来源尝试认证申请，则拒绝申请。

# 结论

颜色可能是头痛，但有一些简单的步骤你可以采取，使一切都容易得多。

现在你知道怎么生存了。告别莫名其妙的错误`No 'Access-Control-Allow-Origin' header is present on the requested resource`。👋👋👋

# [t1【学分】](#cr%C3%A9ditos-%EF%B8%8F)

*   [你的 CORS 和 API 网关生存指南](https://serverless.com/blog/cors-api-gateway-survival-guide/)，escrito origination por[Alex DeBrie](https://twitter.com/alexbdebrie)****