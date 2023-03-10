# 在 AWS lambda 中创建和部署一个无服务器应用程序

> 原文：<https://dev.to/pcmagas/make-and-deploy-a-serverless-application-into-aws-lambda-1ijd>

在我的工作中，我们需要一个解决方案来编写、维护和部署 aws lambdas。无服务器框架是一个 nodejs 框架，用于制作和部署无服务器应用程序，如 AWS Lambdas。

因此，我们选择无服务器应用程序的唯一原因是:

*   通过环境变量轻松管理配置环境。
*   很容易通过 git 保存 lambda 设置的记录和更改历史，所以我们可以杀死犯了错误的人。(好吧好吧开玩笑，没有人类被杀；)...还没)
*   因为它也是 node.js 框架，所以我们可以使用用于单元和集成测试的普通框架。
*   同样由于上述原因，我们也可以使用 nodejs 工具和无服务器框架提供的工具的组合来管理和部署依赖项。
*   Ce 可以具有单个易于维护的代码库，而不需要重复的代码。

# 安装无服务器

```
sudo -H npm i -g serverless 
```

Enter fullscreen mode Exit fullscreen mode

(对于 windows ommit 的`sudo -H`部分)

# 我们的第一个λ

如果没有，我们需要创建我们的项目文件夹并初始化 node.js 项目:

```
mkdir myFirstLambda
cd myFirstLambda
npm init
git add .
git commit -m "Our first project" 
```

Enter fullscreen mode Exit fullscreen mode

然后安装`serverless`作为开发依赖项，我们需要它，因为在协作项目中，它将安装所有需要的工具来部署和运行项目:

```
npm install --save-dev serverless 
```

Enter fullscreen mode Exit fullscreen mode

然后运行下面的命令来引导我们的第一个 lambda 函数:

```
serverless create --template aws-nodejs 
```

Enter fullscreen mode Exit fullscreen mode

使用该命令生成了 2 个文件:

*   `handler.js`其中包含我们的 aws lambda 处理程序。
*   `serverless.yml`其中包含了所有的部署和运行设置。

然后在`handler.js`上，用代表功能的相应名称更改功能`module.exports.hello`。为了我们的目的，我们将保持现状。我们可以通过命令
在本地运行 lambda 函数

```
 sls invoke local --stage=dev --function hello 
```

Enter fullscreen mode Exit fullscreen mode

它将显示函数 hello 在`handler.js`的返回值。将上面的命令作为一个`start`脚本放到`scripts`部分的`package.json`中也是一个好主意。

# 部署 aws

首先，我们需要指定 lambda 名称。因此，我们需要相应地修改`serverless.yml`，以便能够指定 AWS lambda 名称。因此，我们将`functions`部分从:

```
functions:
  hello:
    handler: handler.hello 
```

Enter fullscreen mode Exit fullscreen mode

成:

```
functions:
  hello:
    handler: handler.hello
    name: MyLambda
    description: "My First Lambda"
    timeout: 10
    memorySize: 512 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们可以将部署的 lambda 列为 aws 控制台的`MyLambda`,也可以指定和共享 lambda 设置。

此外，最好在`environment:`部分用以下设置指定环境变量:

```
 environment: ${file(./.env.${self:provider.stage}.yml)} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们可以将`stage`用于每个部署环境，每个设置都将从。环境文件。同样在部署时，将使用`.env`文件，以便能够指定**部署的**λ环境变量。

运送一个模板也是一个好主意。env 文件命名为`.env.yml.dist`，所以每个开发人员将需要做:

```
cp .env.yml.dist .env.dev.yml 
```

Enter fullscreen mode Exit fullscreen mode

并填充适当的设置。对于生产，您还需要做:

```
cp .env.yml.dist .env.prod.yml 
```

Enter fullscreen mode Exit fullscreen mode

然后排除这些要部署的文件，除了 stage 参数提供的 on(将在下面看到):

```
package:
  include:
    - .env.${self:provider.stage}.yml
  exclude:
    - .env.*.yml.dist
    - .env.*.yml 
```

Enter fullscreen mode Exit fullscreen mode

然后使用命令进行部署:

```
sls deploy --stage ^environment_type^ --region ^aws_region^ 
```

Enter fullscreen mode Exit fullscreen mode

正如所见，下面的模式是:`.env.^environment_type^.yml`其中`^environment_type^`是在`sls invoke`和`sls deploy`命令中由`--stage`参数提供的值。

我们也可以根据环境使用这些设置来指定 lambda 名称:

```
functions:
  hello:
    handler: handler.hello
    name: MyLambda-${self:provider.stage}
    description: "My First Lambda"
    timeout: 10
    memorySize: 512 
```

Enter fullscreen mode Exit fullscreen mode

其中`${self:provider.stage}`从`--stage`参数中取值。适用于在`serverless.yml`档遇到`${self:provider.stage}`的情况。