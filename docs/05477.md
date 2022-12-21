# 构建 Ruby 无服务器 CI & CD 管道

> 原文：<https://dev.to/codegram/building-a-ruby-serverless-ci-cd-pipeline-35m5>

*最初发表于 [Codegram 的博客](https://www.codegram.com/blog/serverless-ruby-ci-cd)T3。*

当面对一个无服务器的项目时，作为开发人员，我们可能最不想担心的就是基础设施或部署。我们只是想确保一旦我们确定我们的应用程序工作正常，就尽可能轻松地部署它。这当然也适用于任何类型的项目，但我认为它特别适合无服务器的项目。

在这个例子中，我们将使用[无服务器框架](https://serverless.com)，它已经做了很多工作，但是我仍然发现我自己在不同的项目之间重复相同的任务和代码，每次我必须创建持续集成&部署管道。

### 管道

那么，这个管道应该是什么样子的呢？我们需要采取哪些步骤来确保一切正常运行？对我来说，它应该是这样的:

1.  开发人员在新的 Git 分支上开发新的特性。
2.  他们创建一个新的“拉”请求来继续推动变更。
3.  持续集成系统运行测试套件，可能还有一些 linters。
4.  当拉请求被他们的同事批准和审查时，它被合并到主分支。
5.  持续部署系统启动(可以与配置项相同)并部署新功能。

在不同的项目中重复这个过程之后，我认为是时候抽象出一些公共代码和最佳实践了，比如处理依赖性、CI 缓存、构建、部署等等。

### 真实世界的例子

继上一篇关于如何用 Ruby 创建无服务器的 GraphQL API 的文章之后，我更新了这个项目，添加了一些测试和使用 CircleCI 的自动部署。看看这个[拉动请求](https://github.com/oriolgual/serverless-ruby-graphql/pull/1/files)，看看有多简单！

首先，我们需要一个 CircleCI 帐户，并用它来设置项目。然后将 AWS 凭证添加到 Circle，这样就可以自动部署它。我建议按照[这些说明](https://serverless.com/framework/docs/providers/aws/guide/credentials/)为你的项目创建一个 IAM 用户。

要添加 AWS 凭证，请转到项目的设置页面，使用从 AWS 控制台获取的值添加`AWS_ACCESS_KEY_ID`和`AWS_SECRET_ACCESS_KEY`变量。因为这是默认的环境变量名，所以无服务器将在部署时使用它们。

之后，我们需要将 CircleCI 的配置添加到项目中:

***。circi/config . yml***

```
version: 2.1

  orbs:
   serverless-ruby: codegram/serverless-ruby@0.0.3

  workflows:
   main:
     jobs:
       - serverless-ruby/test
       - serverless-ruby/deploy:
           requires:
             - serverless-ruby/test
           filters:
             branches:
               only: master 
```

Enter fullscreen mode Exit fullscreen mode

这个文件使用了我们的 [Circle CI 无服务器 Ruby orb](https://circleci.com/orbs/registry/orb/codegram/serverless-ruby) (稍后将详细介绍)，然后设置了一个包含两个任务的工作流:一个运行测试套件，一个在代码合并到`master`分支时部署代码。

最后，我们需要调优`serverless.yml`文件，以便获得只有这几行代码的较小版本:

**无服务器。yml**

```
package:
  exclude:
    - '**'
  include:
    - app/**
    - vendor/**
    - '*.rb' 
```

Enter fullscreen mode Exit fullscreen mode

这几行告诉无服务器框架排除根目录中的所有文件，只包括我们在`app`的应用程序代码、`vendor`的打包 gems 和根目录中的所有 Ruby 文件，因为那里是 [app.rb](https://github.com/oriolgual/serverless-ruby-graphql/blob/master/app.rb) 所在的位置(项目的入口点；AWS Lambda 调用来处理每个请求的文件)。

就这样，我们现在有了一个完整的 CI & CD 无服务器管道🎉。

### CI & CD 流程讲解

之前我提到过我们使用了 CircleCI 圆球。对于那些不知道的人:orb 是一种共享公共配置、作业、命令等的方式。在 CircleCI 配置文件中。通过这种方式，您不必在项目之间重复相同的代码和设置，并且可以在 orb 更新时受益。我们创建它不仅是为了避免重复代码，也是为了分享一些最佳实践和解决这类项目中的常见问题。

在示例项目中，在使用 orb 之前，部署过程远非理想。Lambda 需要将你所有的代码和依赖项打包到一个 ZIP 文件中，因为我们在 Ruby 领域，这意味着也要打包我们的宝石。通常当使用 Bundler 时，你所有的 gem 源码都存储在你的主目录中，但是当你需要把它们包含在 ZIP 文件中时，这就不起作用了。所以我需要一个无服务器插件来挂钩到包步骤，运行`bundle install --deployment`来包含项目文件夹中的所有 gems 内容，然后部署，然后通过从项目根目录中删除`vendor`和`.bundle`目录来清理*混乱的*。

这种方法很容易出错，因为我是从自己的计算机上部署的，而 gem 依赖项不是在它们运行的相同环境中构建的。

那么，球体是做什么的？

*   使用 Docker 构建 gem 依赖项，这允许 gem 具有本机扩展，因为它们将在与 AWS Lambda 相同的环境中编译。
*   有效地管理所有的缓存，以便在运行之间快速构建，并且您不会浪费时间等待它们。
*   如果您的项目使用 DynamoDB，它将在测试套件运行之前安装依赖项并启动数据库。
*   使用`rspec`运行测试套件，并使用`RspecJunitFormatter`上传测试结果
*   使用`serverless`部署项目，并在每次提交给 master 时排除所有测试和开发依赖。

所有这些步骤都可以迁移到您最喜欢的 CI 系统中，因为它们不依赖于 CircleCI。

我对最终的结果非常满意，现在我们的无服务器 Ruby 项目部署得非常快，并且确信一切正常。一个改进可能是为每个拉请求创建不同的部署，这样我们就不必从我们的计算机上部署开发环境，但这是另一个故事了！