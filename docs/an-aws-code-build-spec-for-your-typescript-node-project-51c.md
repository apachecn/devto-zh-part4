# typescript 节点项目的 AWS 代码生成规范

> 原文：<https://dev.to/antonioperez/an-aws-code-build-spec-for-your-typescript-node-project-51c>

[![AWS Code Build](img/747c7b0843c8c0ab2beb99602c5f2196.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VMTS7yDV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://softwaresurvivor.com/wp-content/uploads/2019/07/baccab28f77c594ee6b73de0224e75bd.png)

AWS 代码构建是对你的 [AWS](https://aws.amazon.com/) 基础设施的一个很好的补充。今天，我们将关注软件部署过程的一个方面。**大楼！**在我们开始之前，让我介绍一下架构的背景和我对 CI/CD 流程的计划。这个帖子后面的项目是用[类型脚本](https://www.typescriptlang.org/)写的一个[node . js](https://nodejs.org/en/)T8】express 项目。API 服务于[弹性豆杆](https://aws.amazon.com/elasticbeanstalk/)。我们希望通过使用 AWS Codebuild 将我们的代码版本自动部署到 elastic beanstalk，然后使用[代码管道](https://aws.amazon.com/codepipeline/)将生成的版本推送到 Elastic Bean Stalk 实例。

**部署烂**。您在本地机器上构建了这个伟大的产品，现在您必须将它部署到大众中。你花了太多时间在产品上，以至于忘了审视部署过程。是 DevOps！能有多难？！…呃..有罪。部署有很多种方式，这在本领域很常见。您可以轻松地花一整天的时间来研究适合您环境的不同部署工具。你试图理解某些产品存在的原因，根据别人固执己见的话权衡每种产品的利弊。也许不同平台之间的集成是可能的。特拉维斯为 AWS？那些是如何设置的？一千个选择和话语带来的死亡。现在有了上面的设置，我们可以专注于我们的代码，让自动化工具去担心构建。我也想把这些都放在 AWS 上。

让我们担心 Git 吧。分支管理很重要，但那是另一天的不同主题。在我的项目中，我有两个特殊的分支，一个是“筹备”分支，一个是“生产”分支。我有两个 AWS 代码构建配置来监视这两个分支的任何变化。如果检测到一个变更，例如，我将我的开发分支合并到产品化中，或者将产品化合并到产品化中，代码构建将被触发，并且管道被执行。一分钟后，我们的项目有了新的发布！神奇！

对于 AWS 代码构建，在 git repo 中需要一个 buildspec.yml 来告诉服务器如何安装和构建您的项目。在本例中，我的包 json 文件有一个名为 build-ts 的 npm 命令，它运行`tsc`。您也可以在这里包含您的 tests 命令，但是我将在以后的帖子中再回到这个问题。

工件部分也很重要。Elastic Bean stalk 希望我们压缩结果的内容，而不是生成的构建文件夹。所以我在这里拿了三样东西。package.json 文件、read me 和 dist 文件夹的内容。

```
//buildspec.yml
version: 0.2
phases:
    install:
        commands:
            - 'npm install'
    build:
        commands:
            - 'npm run build-ts'
artifacts:
    type: zip
    files:
        - package.json
        - package-lock.json
        - 'dist/**/*'
        - LICENSE
        - README.md
        - .npmrc 
```

前往 AWS 管道设置管道。当您设置管道时，您可以设置源代码，然后再设置构建项目。这比每个步骤都单独设置，然后再将它们连接起来要容易得多。我计划在我的下一篇文章中详细展示每个步骤，但是希望您已经到了需要构建规范或者只是这种类型的 AWS 基础设施的概述的时候了。编码快乐！

[![Code Pipeline](img/619dd594563bd43c222abf5f38e4ffa6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lWNhPAuS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://softwaresurvivor.com/wp-content/uploads/2019/07/a6f8ed3dbf2b27748e0f7ae332f821b6.png)