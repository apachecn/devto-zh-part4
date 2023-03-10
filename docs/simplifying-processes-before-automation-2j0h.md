# 自动化之前简化流程

> 原文：<https://dev.to/saqibyawar/simplifying-processes-before-automation-2j0h>

我的经理最近找到我，把一个自动化项目放在我的腿上(Yaaay！！).没有任何问题的背景，项目的期望或任何规划，这个请求是我多年来见过很多次的。

随着对 DevOps 角色的认识超过对 DevOps 实践的认识，这种模式现在很常见。雇佣一个“开发人员”，给他“自动化世界”的任务，你就有了 CI/CD...最终。

不对！你将使你的过程自动化，但是如果它们一开始就是一团糟，你只是增加了另一个团队和更多的工具和代码。自动化是流程的增强，而不是替代。

我们以应用配置为例。使用 vault 等密钥管理工具，并将它们集成到您的管道中，您可以自动将配置推送到您的应用程序。太神奇了！！！每次部署节省了 10 分钟。但是底层的过程呢？配置生成、安全共享和跟踪 it 变更。你知道，这个过程需要几天时间和几个团队的门票才能完成。

大多数团队并不关注这些“预部署”过程，因为它们的可见性很低，所以影响是隐藏的。也就是说，直到出了问题。现在您需要回滚，但是您没有跟踪更改。您需要更新这些值，但您不是生成它们的人。您需要在严格的 SLA 内完成所有这些工作，并遵循所有必要的安全协议。

简而言之，解决了错误的问题。随着微服务架构越来越受欢迎，这个问题的规模可能是巨大的(最近与 76 个不同的微服务合作，配置管理是一个噩梦，直到我们采用了 [ConfigTree](https://configtree.co/) )。

流程是需要修正的，自动化总是可以开发和增强的。任何“DevOps 团队”的主要焦点不应该是引入自动化构建或 CI/CD，而是底层过程的重新工程。

实现 DevOps 转换的途径不是从代码开始的。它从白板开始，几乎总是一团糟！！！