# 无服务器 YAML 文件剖析

> 原文：<https://dev.to/_rich/anatomy-of-a-serverless-yaml-file-39ie>

在过去几年中，创建无服务器基础架构变得比以往任何时候都更容易。借助 AWS、Azure 和 Google 云平台，开发人员和工程师在部署网站和应用程序时几乎拥有无限的能力。[无服务器框架](https://serverless.com/)，一个用于创建无服务器架构的流行框架，使得利用最大的云计算平台变得更加容易。他们通过使用 [YAML](https://learn.getgrav.org/16/advanced/yaml) 将云架构配置抽象为更简单的形式来实现这一点。在这篇文章中，我们将更详细地看一下 **serverless.yml** 文件，看看它是如何被用来建立一个无服务器架构的。

## Serverless.yml

yml 是无服务器应用程序的核心。这个文件描述了整个应用程序基础结构，从编程语言到资源访问。

这个 YAML 文件的主要部分是提供者。在 serverless.yml 配置中，您可以选择使用 AWS、Google Cloud 或 Microsoft Azure 作为您的无服务器提供商。您还可以指定想要使用的编程语言。在下面的例子中，我指定我将在 Python 3.7 中使用 AWS。

**示例 serverless.yml:**

```
service: my-first-serverless-app
provider:
  name: aws
  runtime: python3.7 
```

上面的 serverless.yml 文件是无限计算能力、数百个无服务器功能和支持您的网站或应用程序的数据库基础设施的基础。让我们更深入地了解一下这种配置。我将使用 AWS 和 python 作为我的基本示例。

## 环境变量

使用 serverless.yml 文件设置要在应用程序中使用的环境变量非常简单。在 provider 下，添加一个环境部分和一个键值列表。进一步看上面的例子，让我们添加一个环境变量，为用户表指定一个表名。

```
provider:
  name: aws
  runtime: python3.7
  environment:
    USER_TABLE: users_table 
```

现在，在我们使用的任何无服务器函数中，环境变量 **USER_TABLE** 可供我们使用。我们还可以在 serverless.yml 文件的其他部分使用这个变量来命名资源或前缀 HTTP 路径。

要稍后在 serverless.yml 中使用此环境变量，请使用以下语法。请注意，这类似于在其他编程语言中遍历对象。

```
${self:provider.environment.USER_TABLE} 
```

## 采购其他文件

通常，您需要将应用程序的配置分离到单独的文件中。无服务器框架有一个非常好的语法来包含来自其他文件的变量。比方说，对于一个开发分支，您希望用户的表被命名为 **dev_users_table** 。有许多方法可以做到这一点，但是为了说明如何获取其他文件，我将向您展示一种方法。

您可以有多个用于特定环境的配置文件。假设您的项目根文件夹中有两个 YAML 配置文件:

示例 **dev.config.yml:**

```
table_name: 'dev_users_table' 
```

示例 **prod.config.yml** :

```
table_name: 'prod_users_table' 
```

您的生产应用程序将使用 **prod.config.yml.** 来获取这个 prod 文件，您可以使用以下语法:

```
provider:
  name: aws
  runtime: python3.7
  environment:
    USER_TABLE: ${file(./prod.config.yml):table_name} 
```

但是，等等，如何在 dev 上部署这个应用程序呢？您必须更改文件来指定 dev，对吗？不，我们可以更新这个配置来利用**阶段**变量。stage 变量是无服务器框架中的一个特殊变量，可用于指定您正在使用的环境。默认情况下，阶段变量是 **dev。**

## 阶段变量

让我们修复我们的 serverless.yml，这样我们就可以使用 dev config 而不用手动更新文件。为此，我们使用 **${opt:stage，self:provider.stage}。**

带有阶段变量的示例**server less . yml**:

```
provider:
  name: aws
  runtime: python3.7
  environment:
    USER_TABLE: ${opt:stage, self:provider.stage}_users_table 
```

既然我们使用了 stage 变量。我们现在不需要 prod.config.yml 文件。我们可以很容易地用 stage 变量作为所有服务的前缀，这样当我们部署应用程序来测试新的服务或功能时，我们不会影响生产服务。

如果我们想使用单独的配置文件，我们可以使用 stage 变量将配置分成单独的文件，如下所示:

```
provider:
  name: aws
  runtime: python3.7
  environment:
    USER_TABLE: ${file(./${opt:stage, self:provider.stage}.config.yml):table_name} 
```

将环境变量分离到文件中是组织使用许多资源(如 S3 桶、数据库表、API 版本路径等)的大型应用程序的好方法。

## IAM 角色声明

如果您使用过 AWS 服务，那么您可能必须先创建一个 IAM 用户或角色。使用 serverless，您可以通过 serverless.yml 文件授予应用程序使用资源的权限。权限配置基本上是用 YAML 写的 AWS Cloudformation。权限位于提供者下的 **iamRoleStatements** 部分。

**为您的应用提供 S3 访问权限的示例:**

如果你有一个名为**牛逼桶名的 S3 桶。**下面的例子将给予你的应用程序对指定桶的完全访问权(读、写、列表等)。

```
provider:
  name: aws
  runtime: python3.7
  environment:
    USER_TABLE: users_table 
 iamRoleStatements:
    -  Effect: "Allow"
       Action:
         - "s3:*"
       Resource:
          - arn:aws:s3:::awesome-bucket-name 
```

**举例说明您的应用 SES:**

如果您有一个需要发送电子邮件的应用程序。你可以像这样给它访问 Amazon SES 的权限:(注意:你需要单独用一个验证过的域设置 SES。)以下示例为您的应用程序提供了使用所有 SES 资源发送电子邮件的权限。

```
provider:
  name: aws
  runtime: python3.7
  environment:
    USER_TABLE: users_table 
 iamRoleStatements:
    - Effect: "Allow"
      Action:
        - "ses:SendEmail"
      Resource: "*" 
```

## 功能

我一直在写这个神秘的“应用”，但我没有解释那是什么。在您的 serverless.yml 文件中，您可以配置要在您的无服务器提供商的平台上创建的函数。对于 AWS，您可以告诉 serverless 创建可以路由到 HTTP 端点的 Lambda 函数。让我们快速看一下如何通过 **serverless.yml** 文件定义一个 [HTTP 端点](https://en.wikipedia.org/wiki/Web_API)。

没有函数，你实际上就没有应用程序。还记得我们在提供者中指定的编程语言吗？它告诉我们的提供者用哪种编程语言来创建我们的函数。(注意:编程语言由提供商决定。您不能选择任何语言。)

例如，让我们创建一个 HTTP 端点，它返回电子邮件地址的 [md5](https://en.wikipedia.org/wiki/MD5) 散列，以及该电子邮件的 [Gravatar](https://en.gravatar.com/) URL。

要定义一个函数，我们需要两样东西:

1.  一个函数名为 **getHash** 的文件我们将把这个文件称为 **handler.py**
2.  无服务器. yml 函数定义

**server less . yml 定义示例:**

```
functions:
  myCustomFunctionName:
    handler: handler.getHash 
```

在第一行，我们有**函数。**这是 YAML 文件的一部分，位于提供商部分。在函数部分，我们有 **myCustomFunctionName。**这个名称将是 AWS Lambda 中的 Lambda 函数名称，而不是代码中使用的名称。

接下来，我们定义**处理程序**。处理程序是使用点符号的函数的路径。这种点符号类似于 Python 中导入的工作方式。**句柄**是文件名， **getHash** 是该文件中的函数名。

这是我们的 handler.py :

```
import hashlib
import json
CORS = {
    'Access-Control-Allow-Origin': '*',
    'Access-Control-Allow-Credentials': True,
}
def getHash(event, context):
    string = event['pathParameters']['string']
    h = hashlib.md5(string.encode('utf8'))
    h = h.hexdigest()
    return {
        "statusCode": 200,
        "headers": CORS,
        "body": json.dumps({
            "hash": h,
            "avatar": "http://gravatar.com/avatar/{}".format(h)
        })
    } 
```

有了定义的文件和名为 getHash 的函数，我们的 serverless.yml 就可以在 AWS Lambda 上创建函数了。在我们部署它之前，我们需要通过 HTTP 启用对这个函数的访问。为了使它成为 HTTP 端点，让我们添加一个 events 部分。

**事件函数示例:**

```
functions:
  myCustomFunctionName:
    handler: handler.getHash
    events:
      - http:
          path: md5/{email}
          method: get
          cors: true 
```

事件部分将我们的功能与 AWS 提供的 API 网关联系起来。在本节中，我们指定 HTTP 端点的路径和方法。有了这个事件配置，你将能够通过路径**/MD5/[some@email.com](mailto:some@email.com)**访问你的 Lambda 函数。响应将是一个 JSON 对象，带有 md5 散列和 gravatar 映像的 URL。

**示例输出** **与我的邮箱** :

```
{
  "hash": "97fbef641c6e4669b6a1ad4ffb3342f5",
  "avatar": "http://gravatar.com/avatar/97fbef641c6e4669b6a1ad4ffb3342f5"
} 
```

您可以更进一步，要求对此端点进行身份验证和速率限制。[更多来自文档](https://serverless.com/framework/docs/providers/aws/events/apigateway/)

现在我们有了事件，我们可以从前端应用程序向这个函数发送 get 请求，它将运行我们的无服务器函数。

## 结论

这篇文章只是触及了 serverless.yml 文件的表面。我希望您对无服务器有更多的了解，并更深入地研究这个框架和技术。无服务器框架，加上可用的云平台，使人们能够以低成本创建可扩展的产品。为应用程序构建基础结构比以往容易得多，可以通过使用简单的配置文件(如 serverless.yml)来创建。

感谢阅读！