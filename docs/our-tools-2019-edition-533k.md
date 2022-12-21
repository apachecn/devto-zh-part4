# 我们的工具- 2019 版

> 原文：<https://dev.to/kylessg/our-tools-2019-edition-533k>

这是我们用来建造[子弹头列车的，我们的特色标志平台](https://bullet-train.io)。通过一些仔细的 VPS 购买，大部分东西都可以完全免费运行！

(移至[此处](https://bullet-train.io/blog/our-tools-2019)

## GitLab 所有的东西

GitLab 非常棒，而且每发布一次都会变得更棒。它最大的优点是集成了一系列不同的工具，让它们协同工作，帮助提高生产力。这也意味着单个 GitLab 实例可以管理开发过程的很大一部分。

我们自己主持 GitLab。这是一个相当大的应用程序，所以我们在谷歌的云平台上运行 3GB 的虚拟机，但如果你想的话，你可以很容易地使用更便宜的 VPS 提供商。GitLab 的开源版本被称为他们的“社区版”，但不要被愚弄，它的功能齐全，只是缺少他们付费产品的一些项目。它通过他们的[综合包](https://about.gitlab.com/install/#ubuntu)很容易安装，更新也只是

`apt-get update; apt-get upgrade`

你也可以像我们一样使用他们的托管产品，它有一个很棒的免费层。

### 源代码

嗯，很明显。我们所有的代码都推送到 GitLab。由于是开源和自托管的，您可以托管的开发人员或私有项目的数量没有限制。

### CI/CD

GitLab 内置了一流的 CI/CD 平台。您可以快速轻松地进行一键部署。要开始运行，您只需做几件事情。

1.  向项目的根路径添加一个`.gitlab-ci.yml`文件。该文件包含构建产品所需的命令。2.注册一个 GitLab Runner，基本上就是一个在某个地方运行的服务，可以从 GitLab 接收作业，运行它们，然后把结果发送回 GitLab。您甚至可以在与 GitLab 实例相同的机器上运行 runner。

## 敏捷项目管理

我们仍然认为特雷罗是最好的。它又快又简单，而且不碍事。话虽如此，他们最近对他们的免费层做了一些改变，所以另一个值得一试的选择是，再次回到 Gitlab。

您可以使用 Gitlab issue tracker 来管理需求，并在看板中查看它们，类似于 Trello。这样做的一大优势是 Gitlab 组件之间的紧密集成。例如，如果您使用消息

`Fixes #252`

推送提交，则该提交将引用问题编号 252。当您将提交合并到主分支时，Gitlab 甚至会将问题标记为已解决！有很多其他的集成使用这种模式。

## 开发/试运行构建

我们正在使用惊人的 [Dokku](http://dokku.viewdocs.io/dokku/) 来运行开发和特色分支。Dokku 是一个神奇的软件，它基本上将你的 VPS 变成了一个迷你 Heroku 平台。创建和销毁特性分支，以及构建和部署开发构建非常简单。我们使用[这个 Docker 映像](https://github.com/IlyaSemenov/gitlab-ci-git-push)来集成 Gitlab CI 和 Dokku。

## 生产托管

这实际上取决于您正在开发的应用程序。对于[子弹头列车，我们的特色标志平台](https://bullet-train.io)，我们实际上拆分了主机:

*   我们的 Web 前端用 Node/JS 编写，部署到[标准 AppEngine](https://cloud.google.com/appengine/docs/standard/) 。它非常便宜(实际上是免费的)而且坚如磐石。
*   我们的 API 是用 [Django/DRF](https://www.django-rest-framework.org/) 编写的，并使用 Postgres 数据库作为其数据存储。我们在 AWS 和弹性豆茎上托管这个。这为我们提供了不错的自动化扩展选项和简单的部署流程。

## 特征标志

嗯，我们肯定会使用我们的功能标志来帮助构建我们的功能标志！为了确保 CI/CD 流程顺利进行，并减少已提交但未部署的代码量，我们在子弹头列车内部使用了我们的[子弹头列车功能标志](https://bullet-train.io)平台。Meta。我们也有一个不错的免费层，所以我们不必自己花钱来运行我们的功能标志。

## 支持，邮件等

全部免费！我们将 FreshDesk 用于我们的客户问题跟踪器，将 [FreshStatus](https://www.freshworks.com/statuspage/) 用于我们的状态页面，将 [Uptimely](https://uptimely.app/) 用于我们的正常运行时间监控/停机警报。

对于电子邮件，我们使用非常棒的 [Migadu](https://www.migadu.com) 处理入站电子邮件，使用 SendGrid 处理出站电子邮件。

对于营销， [MailerLite](https://www.mailerlite.com/) 管理滴滴活动，免费的[对讲机](https://www.intercom.com/)进行应用内聊天。