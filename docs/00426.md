# 使用无服务器创建和部署 Azure 功能

> 原文：<https://dev.to/mydiemho/create-and-deploy-azure-functions-with-serverless-20ai>

> 本文是 [#ServerlessSeptember](https://dev.to/azure/serverless-september-content-collection-2fhb) 的一部分。在这个无服务器的内容集合中，您可以找到其他有用的文章、详细的教程和视频。9 月份，每天都有来自社区成员和云倡导者的新文章发布，没错，每天都有。
> 
> 在[https://docs.microsoft.com/azure/azure-functions/](https://docs.microsoft.com/azure/azure-functions/?WT.mc_id=servsept_devto-blog-cxa)了解更多关于微软 Azure 如何实现你的无服务器功能。

# 概述

以前，创建 Azure 功能最常见的方式是通过[门户](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-azure-function)或使用 [azure-cli](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-azure-function-azure-cli) 。

当使用这些工具来创建和部署 Azure 功能时，您必须首先手动部署依赖的资源

*   资源组
*   存储帐户
*   应用服务计划

您还必须编写自己的绑定，并将它们放在特定的位置，以便函数能够工作 <sup>[1](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#folder-structure)</sup> 。例如，如果您有一个`hello` http-trigger 函数，您将需要一个包含以下内容的`hello/function.json`文件

```
#  hello/function.json  {  "disabled":  false,  "bindings":  [  {  "type":  "httpTrigger",  "direction":  "in",  "name":  "req",  "authLevel":  "anonymous"  },  {  "type":  "http",  "direction":  "out",  "name":  "res"  }  ],  "entryPoint":  "sayHello",  "scriptFile":  "../src/handlers/hello.js"  } 
```

幸运的是，有一种更简单的方法可以做到这一点。无服务器 azure-functions 插件允许你快速创建和部署功能，而不需要额外的工作。

> 目前，该插件只支持节点，其他语言的支持正在到来。

* * *

## 先决条件

### Node.js

Serverless 是一个 Node.js CLI 工具，所以你需要在你的机器上安装 Node.js。

### 无服务器

确保安装了[无服务器](https://github.com/serverless/serverless)，并且您至少在`1.53`
上

```
 npm i -g serverless 
```

```
➜ sls -v
Framework Core: 1.53.0
Plugin: 3.1.0
SDK: 2.1.1
Components Core: 1.0.0
Components CLI: 1.2.3 
```

一旦安装好，无服务器 CLI 可以用`serverless`或
的简写`sls`命令调用。

```
$ sls -h

Commands
* You can run commands with "serverless" or the shortcut "sls"
* Pass "--verbose" to this command to get in-depth plugin info
* Pass "--no-color" to disable CLI colors
* Pass "--help" after any <command> for contextual help 
```

* * *

## 创建功能

### 命令

使用`create`命令，我们可以指定一个可用的[模板](https://serverless.com/framework/docs/providers/azure/cli-reference/create#available-templates)。对于这个例子，我们使用带有`--template`或简写`-t`标志的`azure-nodejs`。

`--path`或简写`-p`是用模板服务文件创建的位置。

```
sls create --template azure-nodejs --path $(whoami)-sample-app 
```

### 输出

上面的命令创建了一个新目录，在我的例子中是`myho-sample-app`，包含以下内容。

```
├── src
|   ├── handlers
|       ├── goodbye.js
|       ├── hello.js
|   ├── .gitignore
|   ├── host.json
|   ├── package.json
|   ├── README.md
|   ├── serverless.yml 
```

### 注

Azure 插件使用以下组合:

*   前缀(如果提供)
*   服务名称
*   地区
*   阶段

在部署时生成资源名称。由于资源名称在 Azure 中必须是唯一的，添加`$(whoami)`会将您的用户名添加到服务名称的
中，从而创建一个唯一的名称。

* * *

## 安装 Azure 插件

上一步创建了一个新目录`<YOUR-USER-NAME>-sample-app`，其中存储了所有与函数相关的代码。`cd`进入目录

运行

```
npm install 
```

* * *

## 本地测试

> 如果您不想在部署之前在本地测试您的功能，可以跳过这一节。

从模板创建的示例应用程序包含两个功能:你好和再见。在部署到 Azure 之前，您可以在本地测试它们。

您需要使用两个终端窗口来完成以下任务。

### 端子 1

```
sls offline 
```

当这个过程运行时，您会注意到已经添加了一些新文件。这些文件是 Azure function 运行所必需的，但在进程退出时将被清理。

### 端子 2

```
sls invoke local -f hello -d '{"name": "Azure"}' 
```

在您的终端窗口中，您应该看到以下响应

```
$ Serverless: URL for invocation: http://localhost:7071/api/hello?name%3DAzure
$ Serverless: Invoking function hello with GET request
$ Serverless: "Hello Azure" 
```

* * *

## 部署到 Azure

一旦你对代码感到满意，下一步就是部署到 Azure。

### Azure 订阅

如果你没有 Azure 账户，可以从[注册一个免费账户](https://azure.microsoft.com/en-us/free/)开始，其中包括 200 美元的免费点数

### 设置凭证

在部署之前，您必须设置一个`service principal`。

#### Azure-cli

确保您已经安装了 [azure-cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)

#### 登录

```
az login
az account list # list all subscriptions
az account set --subscription <SUBSCRIPTION_ID> # choose the one you want 
```

#### 生成服务委托人

下载这个[脚本](https://raw.githubusercontent.com/serverless/serverless-azure-functions/master/scripts/generate-service-principal.sh)，运行它，并按照打印的说明进行操作。

该脚本将生成、提取所需的凭据，并将其写入一个文件，然后您可以将该文件作为环境变量导出。

```
# Download the file and name it `sp.sh`
curl https://raw.githubusercontent.com/serverless/serverless-azure-functions/master/scripts/generate-service-principal.sh -o sp.sh

# skip this if you're on windows
chmod +x sp.sh

# run the script
./sp.sh 
```

### 部署

部署到 Azure 就像运行下面的命令
一样简单

```
# if you want more logging info, uncomment the next line
# export SLS_DEBUG=*

sls deploy 
```

#### 幕后

1.  该插件创建了一个 arm 模板，用于部署所有依赖资源

    *   资源组
    *   应用服务计划
    *   存储帐户
2.  一旦基础设施启动并运行，压缩后的源代码将被部署到功能应用

```
➜ sls deploy
Serverless: Parsing Azure Functions Bindings.json...
Serverless: Building binding for function: hello event: httpTrigger
Serverless: Parsing Azure Functions Bindings.json...
Serverless: Building binding for function: goodbye event: httpTrigger
Serverless: Packaging service...

...

Serverless: -> Deploying ARM template...

...

Serverless: -> ARM deployment complete Serverless: Deploying serverless functions...
Serverless: Deploying zip file to function app: sls-wus-dev-myho-sample-app
Serverless: -> Deploying service package @ /Users/myho/dev/work/github.com/mydiemho/azure-utils/myho-sample-app/.serverless/myho-sample-app.zip

...

Serverless: Deployed serverless functions:
Serverless: -> goodbye: [GET] sls-wus-dev-myho-sample-app.azurewebsites.net/api/goodbye
Serverless: -> hello: [GET] sls-wus-dev-myho-sample-app.azurewebsites.net/api/hello 
```

### 测试部署的功能

您可以通过直接访问 url 或者使用`invoke`命令来测试部署的功能。

```
sls invoke -f hello -d '{"name": "Azure"}' 
```

* * *

## 总结起来

恭喜你！你已经创建并部署了你的第一个 Azure 无服务器功能。

* * *

## 下一步

除了 Http-trigger 函数之外，你还可以做更多的事情。

### [API 管理](https://docs.microsoft.com/en-us/azure/api-management/)

您可以通过配置`serverless.yml`中的`apim`部分将 APIM 添加到您的功能中。生成的文件已经包含了这一点，只需取消对该部分(第 33-59 行)的注释并重新部署来尝试一下。

1.  APIM 构型

```
# serverless.yml

apim:
  apis:
    - name: v1
      subscriptionRequired: false # if true must provide an api key
      displayName: v1
      description: V1 sample app APIs
      protocols:
        - https
      path: v1
      tags:
        - tag1
        - tag2
      authorization: none
  cors:
    allowCredentials: false
    allowedOrigins:
      - "*"
    allowedMethods:
      - GET
      - POST
      - PUT
      - DELETE
      - PATCH
    allowedHeaders:
      - "*"
    exposeHeaders:
      - "*" 
```

1 .部署输出

```
➜ sls deploy

...

Serverless: Starting APIM service deployment
Serverless: -> Deploying API keys
Serverless: -> Deploying API: v1
Serverless: -> Deploying API Backend: myho-sample-app-backend => https://sls-wus-dev-myho-sample-app.azurewebsites.net/api
Serverless: -> Deploying CORS policy: v1
Serverless: -> Deploying API Operations: sls-wus-dev-797b03-apim
Serverless: --> GET-hello: [GET] https://sls-wus-dev-797b03-apim.azure-api.net/v1/hello
Serverless: --> GET-goodbye: [GET] https://sls-wus-dev-797b03-apim.azure-api.net/v1/goodbye
Serverless: Finished APIM service deployment 
```

> 您将不能使用`invoke`命令来测试 APIM 设置

### 附加触发器

除了 http-trigger 函数，还支持以下触发器

1.  存储 Blob
2.  存储队列
3.  计时器
4.  服务总线队列
5.  服务总线主题
6.  事件中心

更多信息，请参见[正式文档](https://serverless.com/framework/docs/providers/azure/events/)

* * *

## 多动手训练

如果你已经到了这一步，并且想了解更多关于 Azure 上的 serverless，微软将在 2019 年 10 月 7 日星期一 [ServerlessConf](https://serverlessconf.io/) 之前在纽约举办一场免费研讨会。报名是@【http://aka.ms/nycworkshop T2】

可能有也可能没有🍦🍦🍦🍦🍦