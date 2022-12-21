# Kubernetes 开源了他们的安全审计。太棒了。

> 原文：<https://dev.to/hayleydenb/kubernetes-open-sourced-their-security-audit-that-s-awesome-4ajf>

原创发布，由我，[此处](https://snyk.io/blog/kubernetes-open-sourced-their-security-audit-what-can-we-learn/)。

* * *

本周早些时候，也就是 8 月 6 日，云计算原生计算基金会(CNCF)发表了一篇博文，详细介绍了他们最近的 [Kubernetes 安全审计](https://www.cncf.io/blog/2019/08/06/open-sourcing-the-kubernetes-security-audit/)。去年，CNCF 开始了他们的安全审计项目，有三个项目: [CoreDNS](https://coredns.io/2018/03/15/cure53-security-assessment/) 、[特使](https://github.com/envoyproxy/envoy/blob/master/docs/SECURITY_AUDIT.pdf)和[普罗米修斯](https://cure53.de/pentest-report_prometheus.pdf)。由于这个试点项目取得了成功，CNCF 正在将其推广到他们生态系统中的其他项目。

Kubernetes 是一个开源的容器编排引擎，用于自动化部署、扩展和管理容器化的应用程序，是 CNCF 生态系统中最大的项目。令人敬畏的是，CNCF 正在优先考虑其旗舰项目的安全。

# 快速外卖

这份报告的全部发现超出了这篇文章的范围，但是我还是想让你对这些发现有一个大概的了解。报告中的许多建议涉及清理代码库、添加进一步的测试和文档，以及使默认设置更加安全。这些基本的建议将使修补和解决发现的问题变得更加容易。值得注意的是，有五个“高严重性”调查结果，包括访问控制、身份验证、计时和数据验证问题。简而言之，这些问题是:

*   一种 PedSecurityPolicy 的访问控制旁路
*   K8s 不便于证书撤销
*   HTTPS 连接没有经过身份验证
*   检查时间，移动 PID 的使用时间问题
*   kubectl cp 中修补不正确的目录遍历

这份报告绝对值得一读，可以在这里找到。

# 安全广度与深度

审计的范围包括以下五个潜在问题的控制系列:网络、加密、身份验证和授权、机密管理和多租户隔离。Kubernetes 是一个大项目，关于安全问题，报告倾向于广度而不是深度。审计是项目安全健康的鸟瞰图。

虽然代码库的某些方面可以从更深入的审查中受益，但我认为这种广度优先的方法是一个非常好的选择。举个例子，如果你去看医生，做一两次随机血液测试，你可能会发现一些关于你健康的有趣的事情，但是你不太可能对你的健康状况有一个全面的了解。相反，如果你从整体上考虑你的健康状况，你会对你的总体健康状况有一个更好的了解，你的医生可能会找到问题进行进一步的调查。

这份报告不仅仅指出了一两个问题(尽管有些人提到了)，而是对 Kubernetes 的健康状况有了一个总体的认识，因此可以选择优先级和目标，以有利于整个项目的安全性和可持续性。我们为五个控制类别中的每一个都推荐了短期和长期目标，并向我们展示了 Kubernetes 的未来。对维护者和社区的好处

很高兴看到如此重要和广泛使用的项目接受安全审计。它为 Kubernetes 规模的其他项目树立了一个很好的榜样，对项目维护者和整个社区都有好处。

CNCF 和 Kubernetes 的维护者对安全审计进行了深思熟虑的投资。安全审计意味着维护者可能比恶意行为者领先一步，可以采取主动的安全立场，而不仅仅是被动的。总是比别人先发现自己的弱点更可取。对于解决在你计划的报告中发现的问题，而不是在随机和可能不合适的时间发现问题，也有很多要说的。修复可以经过深思熟虑的优先排序和处理，如果漏洞通过其他方式被披露，您不会像现在这样争分夺秒。在进行审计和公开调查结果的过程中，CNCF 和 Kubernetes 的维护者已经在这个领域树立了榜样，并将激励其他项目效仿他们进行和发布安全审计。

此外，通过开源发现，社区成员不仅在短期和长期目标实现时受益，而且他们也立即受益。这种好处来源于几个方面。首先，用户可以选择使用 Kubernetes，因为他们相信 CNCF 和项目的维护者都非常重视安全性。其次，用户可以查看调查结果，并为他们的单个项目做出更明智的选择。最后，他们可以利用审计中包含的所有资源，包括[白皮书](https://github.com/kubernetes/community/blob/master/wg-security-audit/findings/Kubernetes%20White%20Paper.pdf)和[威胁模型](https://github.com/kubernetes/community/blob/master/wg-security-audit/findings/Kubernetes%20Threat%20Model.pdf)，对于希望提高他们的 Kubernetes 或安全技能的人来说，这些都是有趣的读物。

# 结论

如果您有兴趣了解像这样的安全审计是如何工作的，或者甚至参与未来的审计，您可以加入专注于安全的 CNCF 特别兴趣小组(或 SIG)。你可以在这里了解更多:[https://github.com/cncf/sig-security](https://github.com/cncf/sig-security)。

祝贺并赞扬 CNCF 承担了如此重要的工作。