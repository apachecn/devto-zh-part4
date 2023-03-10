# CI/CD:持续部署还是持续交付？

> 原文：<https://dev.to/ruthmoog/ci-cd-continuous-deployment-or-continuous-delivery-4abo>

# CI/CD:持续部署还是持续交付？

我听说过持续部署和持续交付这两个术语可以互换使用，尽管它们*不是同一个*。据我所知，尽管如此，它们被理解为同义词还是很常见的。这篇文章将简化 CI/CD 的含义，并阐明它们的区别和好处。

## 先说第一件事:什么是持续集成？

最简单地说，CI 意味着经常将代码变更集成到主分支中。然而，通常将您的版本控制系统与一个持续集成服务器连接起来，它将自动执行您的测试，并为您的项目提供性能度量。您可以在您的单元和集成测试之上添加代码质量、测试覆盖和样式的测试——但是如果任何拉请求不满足您的阈值，它就不能被添加到主代码中。只有符合您团队标准的代码才会自动集成到 master 中。

> 💡一些开发人员喜欢使用极端的反馈设备(XFD)作为他们项目健康状况的有形表示。我听说过交通灯显示，喷泉，土豆头先生，火箭发射器，被用来指示建造状态。

流行的 CI 工具有 [Travis CI](https://travis-ci.org/) 、 [Circle CI](https://circleci.com) 、 [Jenkins](https://jenkins.io/) 。到目前为止，我一直在我所有的 CI 回复中使用 Travis CI 它对开源项目是免费的，是 2017 年 GitHub 上使用最多的 CI 工具[。](https://github.blog/2017-11-07-github-welcomes-all-ci-tools/)

CI 的好处包括获得自动化的(频繁的)反馈，对主分支上的代码有信心，以及增加协作，每个人都从最新版本开始工作——这也减少了用单个 git pull 引发大量冲突的风险。耶！

## 连续交货

通过连续的*交付*，您可以手动地将您的特性部署到生产环境中，并且，您可以使用特性标志来部署代码，而无需向公众发布。这为分阶段变更提供了一定的安全性，尤其是当你的新特性可能会产生巨大影响时——你可以分散风险！随着持续交付，特性发布的频率可能会降低(每天、每周、每月...)比它们与主分支集成在一起要多。

## 连续部署

通过持续的*部署*，无论何时您的代码被集成到主分支中，它都会被自动部署。您可能希望好的测试乐于自动发布您的代码，因此这种方法适合于 TDD。您的集成率应该不会受到影响——代码很少且经常被部署。

在我的一些项目中，我设置了 Heroku 来收听我的 GitHub 回购。当 Travis CI 完成检查并变绿时，我的更新将自动上线:这让我少做了一件事，而且这非常令人满意。

> “持续部署应该是大多数不受法规或其他要求约束的公司的目标。”- [卡尔·考姆](https://puppet.com/blog/continuous-delivery-vs-continuous-deployment-what-s-diff)

我喜欢向开发人员询问他们的 CI/CD 实践，以深入了解他们的团队。好吧，期望连续部署无处不在是不合适的(想想医疗保健或航空)，但对我来说，连续部署是梦想:做得好，它表明一个团队对他们的代码库有信心，对发布放松(因为它们总是发生，它们没什么大不了的)，并且平稳地操作。