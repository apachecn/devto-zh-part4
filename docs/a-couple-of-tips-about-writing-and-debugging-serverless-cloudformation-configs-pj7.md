# 关于编写和调试无服务器云结构配置的一些技巧

> 原文：<https://dev.to/dvddpl/a-couple-of-tips-about-writing-and-debugging-serverless-cloudformation-configs-pj7>

一旦您开始构建无服务器应用程序，您需要——为了简单和明智——开始使用一些工具，允许用代码描述您的堆栈。它可以是无服务器框架、AWS SAM、Terraform、，但无论如何，你将编写项目的配置，以便它可以被轻松部署(和版本控制)。

在我们的项目中，我们目前使用无服务器框架，我发现使用它非常简单。直到出了问题，然后您努力使用 yml(或 yaml)文件来找出原因。

与 YML 相比，我的一个主要痛点是**缩进**。大多数情况下，当某些东西停止工作时，是因为你在不应该的地方添加了一个空格，或者你以错误的方式嵌套了你的结构。

比如这个作品:

```
functions:
  myLambdaFunction:
    handler: src/index.handler
    name: my-awesome-lambda 
```

Enter fullscreen mode Exit fullscreen mode

但这并不:

```
functions:
  myLambdaFunction:
    handler: src/index.handler
     name: my-awesome-lambda 
```

Enter fullscreen mode Exit fullscreen mode

在 4 行中，您可能会立即看到错误，但是在一个长文件中，这可能会很棘手。

幸运的是，一旦你尝试部署它，你会得到一个警告
[![Bad Mapping in yml](img/133a9ba7f619e64086f37f63a4518d24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MYJ0gzPV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ysfs6j3p0fyl9ph7goii.png) 
，但是*有时*，取决于你描述的结构，缩进可能仍然有效。

事实上，缩进是“节点”开始和结束的唯一“标记”,尽管缩进在形式上是正确的，但您却意外地修改了嵌套对象的结构。

几天前，我花了一点时间才弄明白为什么我们的 iamRoleStatements 不再应用于我们的函数。
这特别棘手，因为当**离线测试 lambda 时，一切都正常，因为使用的凭证是安装在你机器上的凭证——因此有非常广泛的策略。**一旦 lambda 被部署，只有通过 yml 应用的策略和角色才会被考虑。

这是我们配置的摘录:

```
service: myAwesomeService
provider:
  name: aws
  runtime: nodejs10.x
  stage: ${opt:stage, 'dev'}
  region: eu-west-1
  securityGroup:
      //  many other lines with some level on indentation here 
  subnets:
      //  many other lines with some level on indentation here 
  vpc:
      //  many other lines with some level on indentation here 
  environment:
      //  some lines describing env variables
  iamRoleStatements:
    - Effect: Allow
      Action:
        - s3:PutObject
      Resource:
        Fn::Join:
          - ""
          - - "arn:aws:s3:::"
            - Ref: S3BucketRepository
            - "/*"
    //  many other lines with some level on indentation here 

functions:
  myLambdaFunction:
    handler: src/index.handler
    name: my-awesome-lambda 
```

Enter fullscreen mode Exit fullscreen mode

在我们在配方中添加了几个自定义属性之前，一切都运行得很好。

```
custom:
  bucketRef: S3BucketRepository 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，没有添加那些在同一级别的*提供者*和函数，*自定义*被添加在*环境*之后(但是带有提供者的缩进)。结果:提供者节点被视为关闭，iamRoleStatments 丢失。

部署时没有警告，本地测试时没有错误。**部署后，由于缺少访问 s3 存储桶的凭证，所有 lambdas 都失败了。**

[![my lambdas are failing i don't know why](img/4515b5ccca7c8f6fa3453126823f6164.png)](https://i.giphy.com/media/3oz8xt2cQDAetun0B2/giphy.gif)

当没有明显原因的错误发生时，或者当 sls 抱怨格式化时，我做的第一件事就是检查配置的整体缩进。不幸的是，我仍然没有为我们的 ide(Intellij IDEA 或 Visual Studio 代码)和在线工具找到一个合适的 YML 插件

*   [https://jsonformatter.org/yaml-formatter](https://jsonformatter.org/yaml-formatter)
*   [https://onlineyamltools.com/prettify-yaml](https://onlineyamltools.com/prettify-yaml)

远非完美。它们给出的结果稍有不同，不能直接用来格式化我们的配置，但是它们都清楚地显示出你的 yml 是否有问题或者缺少一些格式约定——例如字符串或者数组。我通常使用这些工具来仔细检查并清理文件。在 Intellij IDEA 中有一个很好的特性，可以让你看到文件的总体结构。如果缩进坏了或者有什么东西嵌套错了，你会马上认出来。

检查结构

*   打开你的 yml 文件
*   按 CMD 7 或选择视图-工具窗口-结构

[![Alt Text](img/210b9e7c9b61cacdc92e42c5fe3d1e02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E4PHtvP4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/szmwl0thw6k2itx8l6ul.png)

真正有助于找出其他错误的是运行`sls print`:这将解析您在 yml 中使用的所有变量和引用，并向您显示将部署到云结构的最终配置。

一旦缩进问题得到解决，旧的权限又像以前一样工作了，关于 S3 存储桶的新策略仍然没有应用。

在这种情况下，我通常会直接在 AWS Web 控制台 UI 中摆弄。

*   转到 Web 控制台，
*   选择您的 Lambda 函数
*   滚动到执行角色面板
*   检查您在无服务器 yml 中定义的角色。

[![Execution Role - console ui](img/b5f66eb6e85d818543de1242d093497e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PRTs_5PD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r7hy75mxfg5oyei5kk6x.png)

*   单击该角色可重定向到身份和访问管理(IAM) UIConsole 中的角色摘要。
*   查看附加到您的 Lambda 的所有策略，基本上是赋予它的不同权限
*   单击您的策略，并将其作为 JSON 查看

[![IAM policies - console ui](img/3475b73e446ee6ea5f86435eeebfc522.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P-JcoQ7q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ticiwum0a32vpj58g6tf.png)

从那里你可以一直玩下去，直接**编辑策略，然后尝试重新运行你的 lambda 来立即检查你的改变的效果**，只有在之后才返回编辑你的 serverless.yml 并重新部署整个栈。

记住——永远试着[收紧反馈回路](https://dev.to/dvddpl/the-biggest-advice-i-could-give-to-another-developer-3jme)！

```
"Statement": [
       {
            "Action": [
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3::::OUR_BUCKET_NAME:/*"
            ],
            "Effect": "Allow"
        }] 
```

Enter fullscreen mode Exit fullscreen mode

当我这么做的时候，我意识到冒号有问题...

[![too many colons](img/412e0973b1a92b7f87d1cfc839ad5bb2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--02HJzZrR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cdzu5g2ca0n7233c9fk4.jpg)

为什么？？？仔细看了一下 **Fn::Join:** 函数，我意识到它就是 js Array.join 这样的连接方法:传递一个字符串数组，使用一个分隔符来进行连接。

通常，您传递一个列来分隔*资源类型:区域:帐户 Id* ，但这对于 S3 存储桶来说是不必要的。

事实上，您可以用不同的方式编写该方法，这通常无助于清楚地了解如何设置您的 yml。

写这个

```
Resource: { "Fn::Join" : ["", ["arn:aws:s3:::", "your-bucket-name", "/*"]]} 
```

Enter fullscreen mode Exit fullscreen mode

这

```
Resource:
                    Fn::Join:
                      - ''
                      - - 'arn:aws:s3:::'
                        - 'your-bucket-name'
                        - '/*' 
```

Enter fullscreen mode Exit fullscreen mode

还是这个:

```
Resource: !Join [ '', [ "arn:aws:s3:::", "your-bucket-name", "/*" ] ] 
```

Enter fullscreen mode Exit fullscreen mode

是绝对等价的。

如果你在 UI 控制台中查看你的 Lambda 的 IAM 角色，你写的会变成:

```
"Resource": "arn:aws:s3:::YOUR_BUCKET_NAME/*" 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
"Resource": ["arn:aws:s3:::YOUR_BUCKET_NAME/*"] 
```

Enter fullscreen mode Exit fullscreen mode

如果不止一个。

顺便说一句。你不需要使用 Fn::Join 来声明你的资源。您也可以直接指定 ARN-(这是您最终将在 IAM 策略中找到的内容。

```
 "Resource": [
                "arn:aws:ssm:YOUR_REGION:YOUR_ACCOUNT_ID:parameter/*"
            ] 
```

Enter fullscreen mode Exit fullscreen mode

对于 S3 来说，情况并非如此，但是对于许多其他资源来说——例如参数存储，您需要指定区域和您的 account id——特别是如果您有多个帐户或者您的项目被划分到不同的区域，那么手动指定所有这些值可能会很繁琐并且容易出错。使用变量和引用可能更难理解，但从长远来看，可以简化配置。

```
 Resource:
        - Fn::Join:
          - ":"
          - - "arn:aws:ssm"
            - ${self:provider.region}
            - Ref: AWS::AccountId
            - "parameter/*" 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，避免硬编码值和配置，而使用 env 变量或引用总是一个好主意。

#### 如此。概括一下:

*   用一些 IDE 插件或外部工具检查 yml 的缩进和格式
*   运行 sls print 查看解析了变量和引用的最终配置
*   在 web 控制台中仔细检查配置，并直接使用它来加速反馈循环

* * *

如果你对我们试图用那些自定义变量和权限实现的目标感兴趣，这里是完整的代码和解释。

#### 为每个部署阶段动态创建一个桶，并授予对其进行操作的一些 Lambdas 相对的读/写权限。

首先声明几个定制变量，根据部署阶段动态确定 bucket 的名称，并为整个配置中使用的 Bucket 资源定义一个固定名称

```
custom:
  bucket: SERVICE_NAME-${self:provider.stage}-events-repository
  bucketRef: S3BucketRepository 
```

Enter fullscreen mode Exit fullscreen mode

然后使用资源描述中的`bucketRef`并将变量名传递给它:

```
resources:
  Resources:

    S3BucketRepository:
      Type: AWS::S3::Bucket
      Properties:
        AccessControl: Private
        BucketName: ${self:custom.bucket} 
```

Enter fullscreen mode Exit fullscreen mode

最后将桶名作为环境变量传递给 lambda

```
functions:
  myLambdaFunction:
    handler: src/index.handler
    name: my-awesome-lambda
    environment:
      BUCKET: ${self:custom.bucket} 
```

Enter fullscreen mode Exit fullscreen mode

但是不要忘记添加角色语句，以便 lambda 可以读取和写入 S3 存储桶。

```
 iamRoleStatements:
    - Effect: Allow
      Action:
        - s3:PutObject
      Resource:
        Fn::Join:
          - ""
          - - "arn:aws:s3:::"
            - Ref: S3BucketRepository
            - "/*" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

Jorge Romero 在 Unsplash 上拍摄的照片