# 30 天 AWS(第 11 天)

> 原文：<https://dev.to/gameoverwill/30-days-of-aws-day-10-1h7b>

欢迎回来伙计们，这疯狂挑战的另一个更新。我几乎完成了这门课程，它比我预期的花费了更多的时间，但是我对我学到的所有东西感到兴奋。

今天我学习了负载平衡、弹性和可伸缩性，这些话题很有趣，因为我不太了解 ELB(弹性负载平衡器)和自动伸缩，这些是认证和现实生活中的重要话题。

## 弹性负载平衡器(ELB)

ELB 是一种允许您在与其相关的 EC2 之间分配流量的服务。

顾名思义，它是一个负载平衡器，允许您在不同可用性区域的多个 EC2 实例之间分配传入流量。ELB 的一个重要特点是增加了应用程序的容错能力，检测不健康的实例并重定向到健康的实例。

[![IMAGE](img/2f8463de85e5f6e5f45967b645ca1282.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aWQxqvbl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a0iwf81iv8ee37uoqamh.png)

上图显示了位于基础设施中间的 ELC，它试图平衡 EC2 实例中的所有请求。

## 自动缩放

从文档“AWS Auto Scaling 监控您的应用程序并自动调整容量，以尽可能低的成本保持稳定、可预测的性能。使用 AWS 自动扩展，可以在几分钟内轻松设置跨多个服务的多个资源的应用程序扩展。”

换句话说，自动缩放是一个基于应用程序获得的流量来添加或删除 EC2 实例的过程。

例如，假设您的应用程序没有 ELB 和自动缩放，那么支持您的 EC2 的最大用户数是 3。当第四个用户使用我们的应用程序时，这可能会崩溃或服务将被拒绝。

[![IMAGE4](img/9defa2beccf8e3f956823dcac40f525f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cIb4ytt---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v4s5hyijv4hkzclubdw4.png)

但是使用自动扩展，当用户达到最大值时，EC2 将自动扩展:

[![IMAGE3](img/e6ecd8708afbd546b4d6faccfba6600f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aGrDIACv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x21o9166zxnangz3de0z.png)

自动扩展是免费的，但将对资源自动扩展供应收费，例如，任何超出免费层的 EC2 供应。

## 53 路

Route 53 是您为 AWS 上托管的网站或应用程序管理和配置 web 域的地方。

它有 3 个主要功能:

1.  *域名注册*:亚马逊 Route 53 让你注册一个域名。
2.  *DNS 服务*:亚马逊 Route 53 把你的域名像 www.mypage.com[一样](http://www.mypage.com)翻译成 IPs 192.160.10.0。
3.  *健康检查*:验证您的域是否可达、可用或正常运行。

今天到此为止，我完成了实验，通过了考试，只剩下一个话题了。

[![PASS](img/2d953a6fb5537df561f61421f376977a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--at8M9CJ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/su1va8t1ng7by5i7xnyn.png)

感谢阅读。