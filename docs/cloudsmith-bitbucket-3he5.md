# Cloudsmith +位元速率

> 原文：<https://dev.to/cloudsmith/cloudsmith-bitbucket-3he5>

在 [Cloudsmith](https://cloudsmith.com) ，我们的使命是尽可能简单明了地将您的包放入我们的系统，在这里您可以随心所欲地管理和分发它们。

为此，我们最近花了一些时间与 Atlassian 的 Bitbucket 团队在柏林的[AppWeek](http://go.atlassian.com/awspace?_ga=2.187063531.2132496434.1565627316-1859711499.1556636245)上构建了一个 [Bitbucket Pipe](https://confluence.atlassian.com/bitbucket/pipes-958765631.html?_ga=2.187063531.2132496434.1565627316-1859711499.1556636245) ，让 Bitbucket 用户可以轻松地将其源代码转化为可分发的包，并在 YAML 的几行代码中部署到 Cloudsmith。

## 工作原理

Pipes 构建在 Bitbucket 优秀的 [Pipelines](https://bitbucket.org/product/features/pipelines?_ga=2.187063531.2132496434.1565627316-1859711499.1556636245) 功能之上，该功能允许用户自动完成构建和部署过程，无论它们可能采取何种形式。

最简单地说，位桶管道是一个 Docker 容器和一些 YAML 格式配置的组合。管道旨在从用户管道中删除重复或冗长的配置。管道可以跨存储库或组织共享，并使许多常见的任务变得微不足道。

## 使用管道

Cloudsmith 管道包含在 Bitbucket 的官方维护管道集合中，默认情况下，任何希望将其包含在管道中的用户都可以使用。

要使用管道，您首先需要一个应用程序或库，它可以打包成 Cloudsmith 支持的格式之一。你可以在 [Cloudsmith 的 Bitbucket 账号](https://bitbucket.org/cloudsmith-io/?_ga=2.193818862.2132496434.1565627316-1859711499.1556636245)中看到 Python 和 Javascript 库的例子。

出于这个例子的目的，我们假设您使用的是上面链接中的 Python 例子。

首先，通过编辑存储库中的 bitbucket-pipelines.yml，确保可以使用 Bitbucket 管道打包代码。您可以通过 Bitbucket 的 web UI 或者使用普通的编辑器和 Git 工作流来实现。

对于示例库，创建新标签时构建包的基本管道如下所示:

```
image:
  name: atlassian/default-image:2

build: &build
  step:
    name: Build Python Package
    image: python:3.7
    script:
    - python setup.py sdist
    artifacts:
    - dist/**

pipelines:
  tags:
    release-*:
    - <<: *build 
```

每当您将适当的标签推送到您的位存储库时，此管道将构建一个新的包。构建好的包随后被丢弃，因为我们还没有添加进一步的指令来告诉 Bitbucket 如何处理它。

## 配置管道

接下来，我们将使用我们的官方管道添加 publish package 到 Cloudsmith。我们需要提供一个 API 密匙，管道可以用它向 Cloudsmith 进行认证。您可以在[位桶文档](https://confluence.atlassian.com/bitbucket/variables-in-pipelines-794502608.html?_ga=2.220600667.2132496434.1565627316-1859711499.1556636245)中找到管道变量的官方文档。

API 密匙应该作为一个名为`CLOUDSMITH_API_KEY`的“安全”变量添加(这样它就不会泄露到日志中)。

[![Alt Text](img/3bf29e70136bed6acb49cc9f2e09e3c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tsWNNBbC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8s9mnjoijz75x4k7irdz.png)

一旦配置了身份验证，就可以将管道配置添加到管道中。

如果使用 web 用户界面来配置您的管道，您可以直接从浏览器中选择 Cloudsmith 管道，从支持的管道列表中选择，如下图所示:

[![Alt Text](img/850ae9d3b503fb7afd19bbfdc5201dc2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o0-q-Btu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h0kbcsieqpsw8xsm6x2l.png)

如果使用您的本地编辑器，您可以遵循 Bitbucket 上的[官方自述文件中的说明。](https://bitbucket.org/cloudsmith-io/publish?_ga=2.220600667.2132496434.1565627316-1859711499.1556636245)

添加后，您的管道 YAML 应该如下所示:

```
image:
  name: atlassian/default-image:2

build: &build
  step:
    name: Build Python Package
    image: python:3.7
    script:
    - python setup.py sdist
    artifacts:
    - dist/**

publish: &publish
  step:
    name: Publish Python Package
    script:
    - pipe: cloudsmith-io/publish:0.1.1
      variables:
        CLOUDSMITH_REPOSITORY: 'cloudsmith/examples'
        CLOUDSMITH_API_KEY: $CLOUDSMITH_API_KEY
        PACKAGE_FORMAT: 'python'
        PACKAGE_PATH: 'dist/*.tar.gz'

pipelines:
  tags:
    release-*:
    - <<: *build
    - <<: *publish 
```

这个配置指示管道将工件推送到 [cloudsmith/examples](https://cloudsmith.io/~cloudsmith/repos/examples/packages/) 存储库，使用我们之前存储的 API 键。我们正在上传一个位于`dist/*.tar.gz`的 python 包。

## 使用管道

就是这样！您的管道现在已经配置好了，可以运行了。您应该能够将新的标签推送到 Bitbucket，并看到您的管道运行。

Bitbucket 的 pipeline UI 提供了作业状态的概览:

[![Alt Text](img/3a3c405fc7c80f9e8c411a4880df9a80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--crH4xEYo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h7bjked14xlpluwt3dss.png)

推送后，您应该会在 Cloudsmith UI 中看到您闪亮的新包，可以使用您喜欢的工具下载和安装了:

[![Alt Text](img/f4025fafe2d4d6d9b20a1fad23e93597.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hAK4wAwz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1tqqd7ckko1h81h2xxzh.png)

## 结论

Cloudsmith 的 Bitbucket pipe 为 Bitbucket 用户提供了最简单易行的方式，将他们的资产推送到 [Cloudsmith](https://cloudsmith.io/repos/) 。我们的官方管道由 Cloudsmith 团队维护，您可以确信，当我们发布所有更改和功能时，它将保持最新。

与往常一样，如果您对管道(或任何其他东西)有任何问题，我们可以通过 [support@cloudsmith.io](//support@cloudsmith.io) 联系您。