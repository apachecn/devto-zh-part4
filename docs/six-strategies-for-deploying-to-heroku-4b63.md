# 部署到 Heroku 的六个策略

> 原文：<https://dev.to/heroku/six-strategies-for-deploying-to-heroku-4b63>

将您的应用程序部署到 Heroku 的方法有很多种——事实上，有很多种，我们想提供一些选择的建议。根据您当前的部署流程、团队规模和应用，每种策略都有不同的优势。选择最佳策略可以加快部署，提高自动化程度，并提高开发人员的工作效率。

问题是:您如何知道哪种方法对您的团队来说是“最好的”方法？在本帖中，我们将介绍六种将应用部署到 Heroku 的最常用方法，以及它们如何适应您的部署策略。这些策略并不相互排斥，您可以将几种策略结合起来，为您的团队创建最佳的工作流。阅读这篇文章将有助于你理解不同的选择，以及如何有效地实施它们。

## 使用 Git 部署到生产

我们的第一种方法不仅是最常见的，也是最简单的:[将代码从 Git 存储库推送到 Heroku 应用程序](https://devcenter.heroku.com/articles/git)。你只需将你的 Heroku 应用作为一个[远程](http://git-scm.com/book/en/Git-Basics-Working-with-Remotes)添加到现有的 Git 存储库中，然后使用 git push 将你的代码发送到 Heroku。Heroku 然后自动构建你的应用程序并创建一个新的[版本](https://devcenter.heroku.com/articles/releases)。

因为这种方法要求开发人员拥有完全的访问权限来手动将代码推送到生产环境中，所以它更适合于生产前的部署，或者适合于具有小型可信团队的项目。

### 优点:

*   易于添加到任何基于 Git 的工作流中
*   支持 [Git 子模块](https://devcenter.heroku.com/articles/git-submodules)

### 缺点:

*   需要访问 Git 存储库和 Heroku 应用程序

## GitHub 集成

如果您的存储库托管在 GitHub 上，您可以使用 [GitHub integration](https://devcenter.heroku.com/articles/github-integration) 将更改直接部署到 Heroku。将存储库链接到 Heroku 应用程序后，推送到存储库的更改会自动部署到应用程序中。您可以为特定的分支配置自动部署，或者从 GitHub 手动触发部署。如果您使用持续集成(CI)，您甚至可以阻止部署到 Heroku，直到您的测试通过。

GitHub 集成对于自动化[管道](https://devcenter.heroku.com/articles/pipelines)也很有用。例如，当一个变更被合并到主分支中时，您可能会部署到一个测试的临时环境中。一旦变更通过验证，你就可以[将应用程序推广](https://devcenter.heroku.com/articles/pipelines#promoting)到生产中。

### 优点:

*   自动部署应用程序并保持其最新
*   与[管道](https://devcenter.heroku.com/articles/pipelines)和[审核应用](https://devcenter.heroku.com/articles/github-integration-review-apps)集成，创建连续交付工作流程
*   如果您使用 CI 服务(如 [Heroku CI](https://devcenter.heroku.com/articles/heroku-ci) )来构建/测试您的更改，Heroku 可以在结果失败时阻止部署

### 缺点:

*   需要对存储库的管理员访问权限，因此它仅对您拥有的存储库有用
*   不支持 Git 子模块

## Heroku 审核应用程序

当引入一个变更时，您可能希望在将它直接部署到生产环境之前进行测试。[查看应用](https://devcenter.heroku.com/articles/github-integration-review-apps)让您将任何 GitHub pull 请求(PR)部署为一个独立的、一次性的实例。您可以演示、测试和验证 PR，而无需创建新应用或覆盖您的生产应用。关闭 PR 会破坏审核应用程序，使其成为您现有工作流程的无缝补充。

### 优点:

*   可以自动创建和更新每个公关应用程序
*   支持 Docker 图像
*   支持 [Heroku 私有空间](https://devcenter.heroku.com/articles/private-spaces)用于测试隔离环境中的变化

### 缺点:

*   要求同时启用[管道](https://devcenter.heroku.com/articles/pipelines)和 [GitHub 集成](https://devcenter.heroku.com/articles/github-integration)

## 用 Docker 部署

Docker 允许您将应用程序捆绑到自包含的环境中，确保它们在开发和生产中的行为完全相同。这也给了你更多的控制语言，框架和库来运行你的应用。为了[向 Heroku](https://devcenter.heroku.com/categories/deploying-with-docker) 部署一个容器，你可以将一个映像推送到 [Heroku 容器注册表](https://devcenter.heroku.com/articles/container-registry-and-runtime)，或者[通过在你的应用程序的`heroku.yml`文件中声明来自动构建映像](https://devcenter.heroku.com/articles/build-docker-images-heroku-yml)。

### 优点:

*   自动生成图像，或者将现有图像推送到容器注册表
*   开发和生产之间的一致性
*   兼容 [Heroku 评论应用](https://devcenter.heroku.com/articles/github-integration-review-apps)

### 缺点:

*   如果你的应用还没有在 Docker 中运行，你需要建立一个映像
*   要求您维护自己的[堆栈](https://devcenter.heroku.com/articles/stack)
*   不支持[渠道促销](https://devcenter.heroku.com/articles/pipelines-using-the-platform-api#performing-a-promotion)

## 使用 Hashicorp Terraform

像 [Hashicorp Terraform](https://www.terraform.io/) 这样的基础设施代码工具有助于管理复杂的基础设施。Terraform 也可以用来部署 Heroku 应用程序。尽管没有得到 Heroku 的官方支持，Terraform 已经被许多 Heroku 用户使用。[使用 Terraform 和 Heroku，](https://devcenter.heroku.com/articles/using-terraform-with-heroku)你可以用一种叫做 HCL 的声明式配置语言来定义你的 Heroku 应用。Terraform 可自动完成 Heroku 应用的部署和管理流程，同时还能轻松协调 Heroku 与您现有的基础设施。另外，Terraform v0.12 现在允许您在 PostgreSQL 数据库中存储远程状态。这意味着您现在可以在 Heroku dyno 上运行 Terraform，并将 Terraform 状态存储在 Heroku Postgres 数据库中。

例如，使用 Terraform 和 Kafka 查看一个参考架构。

### 优点:

*   自动化 Heroku 应用部署
*   允许您将 Heroku 应用程序部署为代码
*   简化大型复杂部署的管理
*   允许您配置多个应用程序、私人空间以及来自其他云提供商(例如 AWS、DNSimple 和 Cloudflare)的资源，以获得可重复、可测试的多提供商架构。

### 缺点:

*   如果你还没有使用它，需要学习地形和编写配置

## “部署到 Heroku”按钮

如果部署您的应用程序就像点击一个按钮一样简单，会怎么样？按下[‘部署到 Heroku’按钮](https://devcenter.heroku.com/articles/heroku-button)，就可以了！它非常适合使用默认设置进行测试，或者帮助培训新开发人员。

此按钮用作快捷方式，允许您从 web 浏览器将应用程序部署到 Heroku。这对于你提供给你的用户或客户的应用程序来说是非常好的，比如开源项目。您可以用不同的设置参数化每个按钮，比如将自定义环境变量传递给 Heroku，使用特定的 Git 分支或提供 OAuth 密钥。唯一的要求是您的源代码托管在 GitHub 库中，并且您将有效的 [`app.json`](https://dev.to/scottw/heroku-appjson-487i-temp-slug-5009239) 文件添加到项目的根目录中。我们甚至听说有一家公司在自述文件中为他们的每项内部服务添加了一个按钮。这迫使他们保持简单的部署流程，并帮助新员工快速了解服务的部署方式。

<figure>[![Deploy](img/c5f2ec62efbee76fd41eb9eff7b5c1c0.png)](https://heroku.com/deploy?template=https://github.com/heroku/node-js-sample) 

<figcaption>一个‘部署到 Heroku’按钮。</figcaption>

</figure>

### 优点:

*   易于添加到项目的自述文件或网页中
*   易于使用:只需点击按钮部署应用程序
*   提供具有预配置默认值、环境变量和参数的模板

### 缺点:

*   不支持 Git 子模块
*   当新的提交被添加到部署它的 GitHub repo 时，通过 button 部署的应用程序不会自动更新
*   对于需要保持最新的应用程序来说，这不是一个好的工作流程，因为按钮只能创建新的应用程序，并且部署的应用程序不会自动连接到它来自的 GitHub repo

## 该选哪个？

您选择的方法取决于您的具体部署流程、需求和应用。对于刚刚起步的小团队来说，由于其简单性，使用 Git 进行部署可能是您的第一次部署。Heroku 按钮同样简单，只需点击一下就可以部署整个应用程序。如果您经常使用持续集成或发布，那么通过在提交代码时进行自动部署，与 GitHub 集成可以进一步简化这个过程。这相对于在 IaaS 系统上部署是一个很大的改进，因为 Heroku 自动管理整个过程。

随着您的需求变得更加复杂，根据需要添加其他策略。当您的应用程序在生产环境中运行，并且您需要质量控制时，您可能希望添加管道来获得审查应用程序、自动化测试和暂存环境的优势。如果你需要一个自定义堆栈，那么你可以用 Docker 来完成。随着您添加更复杂的基础设施组件，然后添加 Terraform。

高级团队将使用多种策略的组合:例如，您可以选择通过从 GitHub pull 请求创建一个 review 应用程序来部署 Docker 映像，测试 review 应用程序，然后使用`git push`手动部署最终版本。

准备好尝试这些方法之一了吗？注册一个[免费 Heroku 账户](https://www.heroku.com/free)并进行测试。