# 云错误配置和密码劫持？

> 原文：<https://dev.to/caffiendkitten/cloud-misconfigurations-and-cryptojacking-154g>

今天我在读关于第一资本违约的文章，里面提到了一些我以前听过的术语，但我不知道这些词是什么意思。它讲述了罪犯是如何利用“云错误配置”并对账户进行“密码劫持”的...我知道我以前听说过这些术语，第一个似乎很明显，但我想知道为什么这些是这个案例的热门话题，所以，让我们来看看。

[![](img/e5ba71cbea67db43ea7961e3e2ec65a7.png)](https://i.giphy.com/media/14mgxYFJHXGmoo/giphy.gif)

## 云误构

术语“云错误配置”非常简单...随着越来越多的公司将其数据转移到“云”上，其环境的配置变得非常重要，以防止私人数据暴露给外界。云配置错误是一个大问题，也是导致许多公司不迁移到云的一个大问题，也是导致云配置错误的人为错误。

像亚马逊网络服务(AWS)这样的公司一直在“对其服务和安全功能进行渐进的改变，以遏制此类数据暴露，包括阻止公共访问组织内所有 S3 资源的能力。”(2)

### 能做什么？

使用一些 AWS 安全工具(或您使用的任何提供商可能提供的安全工具)，如 AWS 控制塔、AWS 安全中心，甚至只是在公司中创建一个标准配置流程来帮助消除手动配置错误。
**AWS 控制塔**是一种“建立和管理一个新的、安全的、多帐户的 AWS 环境的方式，该环境基于 AWS 在与数千家迁移到云的企业合作中积累的最佳实践。“(3)
AWS Security Hub 是一项服务，它提供了一个单一的位置来聚合、组织和优先排序来自多个 AWS 服务的安全警报或发现，并提供了高优先级安全警报和 AWS 帐户合规性状态的综合视图。(4)

## 密码破解

加密劫持是一个更大的恶意，因为它“是未经授权使用他人的计算机来挖掘加密货币。”(5)这种情况就像其他恶意软件进入电脑一样...用户点击一个链接，将可执行代码下载到他们的电脑中，或者一个网站被 JavaScript 劫持，在页面加载时自动执行，然后噗！用户的计算机现在正在后台挖掘加密货币(可能)而用户甚至不知道它正在发生。

我发现密码劫持很有趣，因为它不一定需要很多技术技能，但更需要进入黑暗网络购买一个工具包，为矿工处理事情。这种类型的恶意软件已经变得比阻止用户使用计算机更受欢迎，因为它可以为未成年人带来更大的利润。

[![](img/e53b6d8d69e91177b1363b5f863085e3.png)](https://i.giphy.com/media/RigoXzvWlvSvVvbTpd/giphy.gif)

#### 参考文献

1.  [https://www . secureworld Expo . com/industry-news/capital-one-hacker-other-companies-起诉书？UTM _ content = 99837444&UTM _ medium = social&UTM _ source = LinkedIn&HSS _ channel = LCP-106644](https://www.secureworldexpo.com/industry-news/capital-one-hacker-other-companies-indictment?utm_content=99837444&utm_medium=social&utm_source=linkedin&hss_channel=lcp-106644)
2.  [https://searchcloudsecurity . techtarget . com/news/252465909/AWS-customers-trough-cloud-misconfiguration-and-data-exposures](https://searchcloudsecurity.techtarget.com/news/252465909/AWS-customers-tackle-cloud-misconfigurations-and-data-exposures)
3.  [https://aws.amazon.com/controltower/](https://aws.amazon.com/controltower/)
4.  [https://AWS . Amazon . com/about-AWS/whats-new/2018/11/introducing-AWS-security-hub/](https://aws.amazon.com/about-aws/whats-new/2018/11/introducing-aws-security-hub/)
5.  [https://www . csoonline . com/article/3253572/what-is-cryptojacking-how-to-prevent-detect-and-recover-from-it . html](https://www.csoonline.com/article/3253572/what-is-cryptojacking-how-to-prevent-detect-and-recover-from-it.html)
6.  [https://www.malwarebytes.com/cryptojacking/](https://www.malwarebytes.com/cryptojacking/)

###### 请注意，我是学生，还在学习。如果我说的不正确，请告诉我。我很想了解更多我可能不完全了解的东西。