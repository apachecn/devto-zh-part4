# phpDay 2019 的亮点

> 原文：<https://dev.to/alsoknownasdrew/highlights-from-the-phpday-2019-3o87>

当我加入[easy . it](https://engineering.facile.it/)工程团队时，我发现最大的惊喜之一就是教育预算。该公司通过支付书籍或参加课程、研讨会和会议的费用来投资开发人员的专业成长。今年我和我的团队决定用这笔预算的一部分，一起参加由 GrUSP 协会组织的意大利 PHP 大会 [phpDay](https://2019.phpday.it/) 。

PhpDay 是一个为期两天的会议，涵盖了关于 PHP、其社区和生态系统的所有主题。今年的会议于 5 月 10 日和 11 日在美丽的维罗纳圣马可酒店举行。

会议包括 3 个主题演讲和 20 多场平行讲座以及一些非常有用的研讨会。

**免责声明:**以下主题演讲和讲座列表并不完整。它们只是我参加过的，觉得很有趣，值得强调的。

## 第一天

### 欢迎来到 PHP 社区| [奈良卡斯伯根](https://twitter.com/xiehan)

Nara 的主题演讲是关于 PHP 社区的(废话！)以及很多我们可以回馈它的方式。她谈到了如何增加我们的参与，包括如何支持本地用户组，在会议现场发挥更积极的作用，并开始为开源生态系统做出有意义的贡献。

[幻灯片](https://speakerdeck.com/xiehan/phpday-welcome-to-the-php-community)

### 从帮手到中间件| [马尔科·皮埃塔](https://twitter.com/Ocramius)

在这次演讲中，Marco 带领我们了解了 PHP 应用程序架构的演变、框架的使用、良好实践、反模式以及过去几年的一般编码风格。本次会议旨在向我们展示继续推进我们软件更美好未来的建议。

[幻灯片](https://ocramius.github.io/from-helpers-to-middleware/)

### 从 0 到与 Kubernetes、Helm 和 GitLab 的连续部署| [Stefano Torresi](https://twitter.com/storresi)

关于如何在 Kubernetes 集群上使用 Gitlab CI/CD 管道启动和运行应用程序的现场演示，包括每个分支的自动发布和手动环境升级。讲座的主题还包括对 Docker、Kubernetes 和 Helm 的容器化的简短介绍，这有助于管理 K8S 资源清单和安装第三方组件。

[幻灯片](https://stefanotorresi.it/talks/k8s-cd/#/)

### Xdebug 3.0 | [吴镇男·雷森斯](https://twitter.com/derickr)

如果你使用 PHP 应用程序，你可以使用 [XDebug](https://xdebug.org/) ，句号。PHP 开发人员最流行和最有用的工具之一的创造者和维护者吴镇男谈到了他在开发和支持过程中面临的一些挑战。他向我们展示了它的内部结构和所有的配置设置。演讲还包括 XDebug 3 的计划和支持它的方法。剧透:有一个 [Patreon](https://www.patreon.com/derickr) 页面，你可以在那里表达你的爱。

[幻灯片](https://derickrethans.nl/talks/xdebug-phpday19.pdf)

### 用 PHP Meminfo 寻找内存泄漏| [Benoit Jacquemont](https://twitter.com/bjacquemont)

Benoit Jacquemont 讲述了 PHP 内存管理的内部原理，并让我们更好地理解了内存泄漏的根源。他的开源 PHP Meminfo 扩展给出了内存中类实例数量的综合视图，对象和其他结构的完整列表，以及它们之间的关系。通过访问这些信息，可以了解为什么某个项目没有从内存中删除，以及如何解决这个问题。

[幻灯片](https://speakerdeck.com/bitone/hunting-down-memory-leaks-with-php-meminfo)

## 第二天

### 20 世纪 60 年代软件管理的教训| [拉里·加菲尔德](https://twitter.com/Crell)

会议的第二天从拉里·加菲尔德的主题演讲开始。Larry 谈到了从《人月神话》(一本写于 20 世纪 60 年代的关于软件项目管理的书)中学到的一些东西，即使在今天的软件开发中也是实际相关的。技术可能发生了巨大的变化，但人却没有😉。

[幻灯片](https://www.garfieldtech.com/presentations/slides-management-60s/phpday2019/#/)

### 带 Bref 的无服务器 PHP 应用| [马蒂厄·拿波里](https://twitter.com/matthieunapoli)

无服务器和 FAAS 绝对是 2019 年的流行语。它们如何与 PHP 生态系统相结合？Matthieu 回答了这些问题，并用案例研究和用于在 AWS Lambda 上部署 PHP 应用程序的开源解决方案 [Bref](https://bref.sh/) 来说明答案。

[幻灯片](https://mnapoli.fr/presentations/serverless-php/serverless-php.pdf)

### MySQL 8.0——不仅好，简直太棒了| [加布里埃拉·德Á·维拉·费拉拉](https://twitter.com/gabidavila)

Gabriela 的会议涵盖了 MySQL 最新版本的新特性和改进。她在解释窗口函数、公共表表达式、性能增强以及对世界上最常用的数据库的更好的字符集支持方面做得非常出色。

[幻灯片](https://www.slideshare.net/gabidavila/php-day-2019-mysql-8-not-only-good-great)

## 最后的思念和感谢

还有许多其他的精彩演讲，包括 PHP 语言的创造者拉斯马斯·勒德尔夫的闭幕主题演讲，所以我建议你查看一下演讲人的完整名单。您还可以在 GrUSP [YouTube 频道](https://www.youtube.com/channel/UCdWnwC8nz_CCFQrmLBrLCVw)上观看所有演示的视频。

我非常感谢所有组织这次会议的组织者、赞助商、演讲者和志愿者。PhpDay 是一个有趣的活动，有很多有趣的话题和杰出的人物。我希望明年能回来！

液体错误:内部

由 [GrUSP](https://www.facebook.com/phpDay-243655080964/) 拍摄的封面图片。