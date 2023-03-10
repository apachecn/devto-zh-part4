# 思考无服务器:用崔琰定义无服务器架构

> 原文：<https://dev.to/thinkserverless/think-serverless-defining-serverless-architectures-with-yan-cui-theburningmonk-2o2a>

崔琰，网名为 theburningmonk，是一名独立顾问、演讲者和博客写手。Yan 也是[生产就绪无服务器](https://www.manning.com/livevideo/production-ready-serverless)的作者和 [Lumigo](https://lumigo.io/) 的开发者支持者。

[![Yan Cui](img/801c32e220e447a7af8d21511a08b617.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yFKe4kWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/di57n3z2rb3rr6ljjjvc.jpg)

*[Think Serverless](https://thinkserverless.dev) 是一份使用无服务器技术的出版物，以采访和关于软件开发未来的故事为特色。[在推特上关注我们](https://twitter.com/_serverless)，然后[加入我们的邮件列表](https://buttondown.email/thinkserverless)！*

#### 你的背景是什么，你在做什么？

我在 AWS 中运行生产工作负载已经将近 10 年了，在过去的 2-3 年里，我一直专注于无服务器领域。我是 AWS 无服务器英雄，独立顾问。我与世界各地的客户合作，帮助他们采用无服务器技术，避免代价高昂的错误。

#### 您采用无服务器架构的第一个项目/工作负载是什么？

2014 年 Lambda 公布后不久，我在 2015 年初用 Lambda 和 Kinesis 实现了一个实时事件处理系统。

#### 关于无服务器模式，现在最让你兴奋的是什么？

能够专注于我的用户想要的东西，而不是我过去为了做我真正需要做的事情而不得不做的所有事情！

#### 你最近用无服务器搭建的最喜欢的东西是什么？

最近，为了满足一些不寻常的操作约束，我和一个客户一起构建了一个自我修复的 kinesis 功能。这是一个有趣的练习，你可以在这里阅读。

#### 您如何定义无服务器架构？

我认为“无服务器架构”是一种不负责管理基础设施的架构——底层服务器、机器映像、修补操作系统、配置负载平衡、自动扩展等等。这些架构围绕 AWS Lambda 等功能即服务(FaaS)平台发展，我们专注于编写业务逻辑，让云提供商处理基础设施。其中许多架构也是围绕事件设计并由事件驱动的，例如，用户登录应用程序、IOT 传感器记录新的数据点，或者文件上传到 S3，等等。

#### 你认为现在无服务器生态系统最大的弱点是什么？

围绕无服务器可观察性的供应商生态系统仍在不断成熟。与运行在容器或虚拟机中的微服务可用的工具相比，对无服务器应用程序的工具支持仍然远远落后。但是事情正在迅速改善。有专注于无服务器的供应商正在努力解决这个问题，如 Lumigo 和 Epsagon。主要迎合虚拟机和容器的供应商也开始关注无服务器。云提供商(如 CloudWatch)的原生解决方案也在不断改进。

#### 采用无服务器对您的组织有什么影响？

系统变得更加可扩展和健壮，也更加安全。功能交付变得更快，团队能够更及时地对客户反馈做出反应。因此，产品和开发团队之间的互动得到了改善。开发人员变得更开心了，因为出错更少了，而且他们完成工作更快了！

#### 你对刚起步的无服务器开发者有什么建议？

向他人学习，不要害怕尝试新事物。你可以通过实验学到很多东西，而且在无服务器的情况下，你只需要为你使用的东西付费，这些实验是很便宜的。

#### 您最喜欢哪些资源来了解无服务器生态系统的更多信息？(人、博客等)

杰里米·戴利的[“无一例外”](https://www.jeremydaly.com/newsletter/)时事通讯是一个很好的起点。此外，阅读本·凯霍或保罗·约翰斯顿的任何文章，[一位云专家的博客](https://read.acloud.guru/)也有许多好文章。我也在[写了很多关于无服务器的](https://medium.com/theburningmonk-com/all-my-posts-on-serverless-aws-lambda-43c17a147f91)。