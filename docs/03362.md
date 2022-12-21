# 带有 Cognito 的 AWS SAM API

> 原文：<https://dev.to/jeffisadams/aws-sam-api-with-cognito-1410>

# [T1】简介](#intro)

这是官方的！AWS 已经认定 Lambdas 是我们的锤子，我们都在四处找钉子。这是一个引人注目的用例。它运行起来很便宜，易于维护，没有基础设施，并且你可以在云中运行可扩展的代码。有很多教程使用各种工具运行 hello world 功能，包括编码和 AWS UI 相关的工具。这些教程通常忽略了为一组函数创建中央 API 网关的能力，也忽略了如何用一个基本的身份验证层来保护您的 API。我的目标是展示一个“hello *Whole Wide* world”的例子，其中包括一些细节。

[![Bees](img/4aaac491dce54fc44801ddc4a4281b21.png "Bees!")](https://res.cloudinary.com/practicaldev/image/fetch/s--35UkP65M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tenmilesquare.com/wp-content/uploads/2019/06/apis-mellifera-bee-beekeeping-56876-1024x683.jpg)

## TLDR

基本回购就是这里的。欢迎 PRs 和建议。

# 我们在建造什么

*   API 网关
*   一个 Cognito 用户池，用于限制对我们某项功能的访问
    *   认知用户
*   一个简单的功能，不受我们的授权层保护
*   由我们创建的授权层保护的简单功能

# 先决条件

*   [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)
*   [AWS 无服务器应用模型 CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)
*   [出发](https://golang.org/doc/install)
*   [命令行访问您的环境](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)

# 潜进去

您可以下载 repo，设置所需的变量(STACK_NAME、STACK_BUCKET、YOUR_EMAIL)并运行`make deploy`来查看这一过程。我们希望能够从零构建到堆栈。我发现 AWS 是一个敏感的野兽，需要围绕微妙的细节不断迭代。为了有条不紊地逐步完成这些迭代，需要有可以重复运行的代码，这一点我怎么强调都不为过。从某个已经损失了几天工作的人身上可以看出，从一开始就设置代码是值得的。

## 用户池/客户端/用户

```
UserPool:
    Type: AWS::Cognito::UserPool
    Properties:
        AdminCreateUserConfig:
        AllowAdminCreateUserOnly: false
        UserPoolName: TestingUsers
        UsernameAttributes:
        - email
        AutoVerifiedAttributes:
        - email
        Policies:
        PasswordPolicy:
            MinimumLength: 6
            RequireLowercase: true
            RequireNumbers: false
            RequireSymbols: false
            RequireUppercase: true
UserPoolTokenClient:
    Type: AWS::Cognito::UserPoolClient
    Properties:
      UserPoolId: !Ref UserPool
      GenerateSecret: false
      ExplicitAuthFlows:
      - USER_PASSWORD_AUTH 
```

Enter fullscreen mode Exit fullscreen mode

我们创建一个用户池和一个用户池客户端。池是用户及其信息的抽象集合。客户端能够使用 SDK 或 CLI 登录。您可能需要额外的客户端(我们还没有 Oauth)和额外的属性，但是这是一个最小的工作集。查看[云信息参考文档](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-cognito-userpool.html)了解更多详情。为了登录，我们将使用 AWS CLI。

```
UserPoolUser:
    Type: AWS::Cognito::UserPoolUser
    Properties:
      DesiredDeliveryMediums:
        - EMAIL
      Username: !Ref YourEmail
      UserPoolId: !Ref UserPool 
```

Enter fullscreen mode Exit fullscreen mode

我们还使用一个环境变量 YOUR_EMAIL 创建您的第一个用户。这将在堆栈创建时向您的电子邮件发送一个临时密码。下面是我们将如何登录的说明(剧透，它与 CLI)。

## API

技术上我们不需要这个。山姆会为我们创造一个。但是当你有两个函数时，你就有了两个完整的 API。我们可以做得更好。

```
ServiceApi:
    DependsOn: UserPool
    Type: AWS::Serverless::Api
    Properties:
      Name: ServiceApi
      StageName: !Ref Version
      Cors:
        AllowMethods: "'*'"
        AllowHeaders: "'*'"
        AllowOrigin: "'*'"
      Auth:
        Authorizers:
          CognitoAuthorizer:
            UserPoolArn: !GetAtt "UserPool.Arn" 
```

Enter fullscreen mode Exit fullscreen mode

创建后，我们使用 API ID 将创建的函数附加到一个逻辑组中。我还设置了 Cors 标题，让它保持开放。除非你明白其中的含义，否则不要这样做。现在，当我们创建我们的功能时，我们可以将它们集中在这个 API 下，并拥有一个更有组织的微服务，而不是一个功能集合。

## 功能

相当基本的声明。下面是未经认证的函数

```
OpenLambdaFunction:
    Type: AWS::Serverless::Function
    Properties:
      Description: Handles the basic request with no need for authentication
      Runtime: go1.x
      Handler: ./dist/open
      Events:
        Get:
          Type: Api
          Properties:
            Path: /open
            RestApiId: !Ref ServiceApi
            Method: GET 
```

Enter fullscreen mode Exit fullscreen mode

和认证的。它们看起来非常相似，但是我想用不同的代码来处理它们。合并成事件和分离的功能都有很好的用例。

```
LambdaFunction:
    Type: AWS::Serverless::Function
    Properties:
      Description: Handles the basic request
      Runtime: go1.x
      Handler: ./dist/authenticated
      Events:
        Get:
          Type: Api
          Properties:
            Path: /
            RestApiId: !Ref ServiceApi
            Method: GET
            Auth:
              Authorizer: CognitoAuthorizer 
```

Enter fullscreen mode Exit fullscreen mode

该函数指定了文件所属的 API 网关、要使用的授权人以及要响应的路径/方法。

## 代码

这可以说是最简单的部分。寄回一张 200 就行了。这两个版本的代码基本上是一样的，只是负载内容有所调整。

```
return events.APIGatewayProxyResponse{
    StatusCode: 200,
    Body:       "{\"message\":\"Marshalling a return body is a problem for another day.  But the request was successful\",\"structure\":\"See this is actually not an error\"}",
    // This is important as part of the CORS config.
    // Again you should know the security implications of CORS before implementing this
    Headers: map[string]string{
        "Content-Type":                 "application/json",
        "Access-Control-Allow-Origin":  "*",
        "Access-Control-Allow-Methods": "*",
        "Access-Control-Allow-Headers": "*",
    },
}, nil 
```

Enter fullscreen mode Exit fullscreen mode

# 构建和部署

因为我们使用 GO，我们需要一个构建和编译过程。我使用一个基本的 Makefile 来编译，使用 GO 并运行 AWS SAM Cli 命令。

您需要设置以下变量:

*   堆栈名称
    *   如何称呼云形成堆栈
*   栈 _ 桶
    *   Sam 将您编译的代码资源上传到一个桶中。我将让您创建一个存储桶并设置这个环境变量
*   您的电子邮件地址
    *   为第一个创建的用户输入有效的电子邮件地址。您可以通过 AWS 门户来实现这一点，但重点仍然是完整的代码验证示例。

一旦设置完毕，运行`make deploy`。假设您可以访问您的 AWS 环境，您将看到构建过程编译代码，将代码上传到 bucket，同时将 SAM 模板转换为 AWS cloudformation 模板，并部署堆栈。这也适用于更新。您将希望查看堆栈创建的输出，以便获得登录所需的 id 和要调用的 API url。为此，我使用了 web 门户，但是您也可以使用 CLI 访问输出。

# 我有一个 API，现在呢？

## 姑且称之为开放端点。

```
curl https://{{Your API ID}.execute-api.us-east-1.amazonaws.com/v1/open
{ message: "This endpoint does not require any authentication", structure: "This field was added just to prove it's not an error" } 
```

Enter fullscreen mode Exit fullscreen mode

这个管用！现在让我们呼叫授权端点

```
curl https://{{Your API ID}.execute-api.us-east-1.amazonaws.com/v1
{"message":"Unauthorized"} 
```

Enter fullscreen mode Exit fullscreen mode

从技术上来说，这是一件好事，但我们可以做得更好。我们需要登录。我们创建了一个令牌客户端，它将响应 SDK / CLI 登录请求。这可能不太安全，但允许我们在没有额外基础设施的情况下登录。

## 登录并获取令牌

我们将使用 AWS cli 登录。首次登录需要更改密码，并遵循质询工作流程。为了减轻痛苦，我创建了一个脚本，您可以调用它来登录并运行密码质询响应。

```
AUTH_CHALLENGE_SESSION=`aws cognito-idp initiate-auth \
--auth-flow USER_PASSWORD_AUTH \
--auth-parameters "USERNAME={{YOUR_EMAIL}},PASSWORD={{password from the email AWS sent you}}" \
--client-id {{Token Client ID}} \
--query "Session" \
--output text`

# Then respond to the challenge
aws cognito-idp admin-respond-to-auth-challenge \
--user-pool-id $USERPOOLID \
--client-id {{Token Client ID}} \
--challenge-responses "NEW_PASSWORD=Testing1,USERNAME={{YOUR_EMAIL}}" \
--challenge-name NEW_PASSWORD_REQUIRED \
--session $AUTH_CHALLENGE_SESSION 
```

Enter fullscreen mode Exit fullscreen mode

要使用这个脚本，从 cloudformation 堆栈中获取输出值并运行以下命令:
`./scripts/login_first.sh {{User Pool ID}} {{Token Client ID}} {{Your Email}} {{Password in the Email AWS sent you}}`

这会将您的密码更改为“测试 1”并让您登录。您将获得一个 JSON Web 令牌或 JWT 令牌，现在您可以使用它来最终调用该死的 API。

# 带回家

现在我们有了 auth 令牌，我们可以将它添加到头中并调用

```
curl -H "Authorization: {{AUTH_TOKEN (The output from the login_first script)}}" https://{{Your API ID}}.execute-api.us-east-1.amazonaws.com/v1

{"message":"Marshalling a return body is a problem for another day.  But the request was successful","structure":"See this is actually not an error"} 
```

Enter fullscreen mode Exit fullscreen mode

# 我们完成了什么？

所以我们现在从零到:

*   userpool authorizer(用户工具作者)
*   API 网关，我们可以在其中放置多个函数
*   在 path /open 中不需要授权的函数
*   在 path / We 下需要授权的函数可以使用 AWS CLI /登录脚本`./scripts/login.sh {{UserPool Client ID}} {{Your Email}} Testing1`登录，并将输出 IdToken 添加到我们的请求中，以便调用我们的 API。

## 推荐阅读

*   [山姆举例](https://github.com/awslabs/serverless-application-model/tree/master/examples/2016-10-31)
*   [认知文档](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html)