# Hackitect 系列第 2 卷——开源网络安全动物园

> 原文：<https://dev.to/sottlmarek/hackitect-series-vol-2-open-source-cybersecurity-zoo-4g70>

# 开源安全动物动物园

正如我在第一篇文章中承诺的[https://www . LinkedIn . com/pulse/hack itect-journey-mission-Marek-% C5 % a1 ottl-ceh-cissp/](https://www.linkedin.com/pulse/hackitect-journey-mission-marek-%C5%A1ottl-ceh-cissp/)这里是网络安全开源动物园。这篇文章不会把你引向已知的和最具市场突破性的开源软件，但是它会引出对黑客的日常工作有用的不同的网络动物。列表不限于我的选择。所以，请随意分享你的东西和想法。

抹去它支持商业的想法。我们需要理解安全变成了功能。本质上的功能需求。然后，我们需要了解安全性是为了支持业务而不是支持。为了实现业务，我们需要忘记我们所知道的，并寻找不寻常的解决方案。

## 果菲莎

我记得这个伟大的工具完成了几个项目。像这样开源之后，内部钓鱼变得简单而有趣。你可以跟踪用户的行为，位置，发送的数据等。一切只需一台服务器和一个开源解决方案(带酷炫的 GUI)。活动数据可以轻松导出为 csv 格式。

运动是用经典的 HTML 和 CSS 编写的，但如果你没有这方面的知识，你可以很容易地复制现有的系统，欺骗你的受害者。电子邮件也很容易创建。系统有预定义模板。编码模板！所有鱼叉式的、有针对性的、大规模的网络钓鱼活动都成了你工作中有趣的一部分。

网络钓鱼大师可以获得有关电子邮件状态的信息。电子邮件可以发送，打开，点击，甚至数据发送(可以匿名- GDPR 准备！).所有这些都在一个包里。

## 波动

法医野兽很难在免费安全办公室发现。是的，这是波动引擎。这个工具是神奇的开源内存取证。最新版本是 2016 年的 2.6 版本。不是最好的，但是你有受恶意软件影响的机器，并希望专注于内存取证，还不错。作为大多数安全工具，这个网络玩具是用 python 编写的。

有兴趣了解更多吗？你喜欢玩和使用互斥值来寻找恶意软件吗？看这里:[https://github . com/Volatility foundation/Volatility/wiki/Volatility-Usage](https://github.com/volatilityfoundation/volatility/wiki/Volatility-Usage)

## Kubebench 和 Dockerbench

如果您正在寻找用于基准测试的完美工具和架构，那么您就在正确的道路上。如果你想在你的 AWS Lambda 或 Azure 功能中运行审计，作为直接向 CloudWatch 报告的无服务器解决方案，你已经达到了云化的网络圣杯。

还记得那个叫 **lynis** 的工具吗？完美的 linux 审计和基准测试工具。易于使用和实施。如果你是集装箱的忠实粉丝，这条双头蛇绝对是你的选择。工具 **Kube-bench 和 docker-bench** 正在提供集装箱化系统的全面审计。Dockerbench 的灵感来自于 CIS(互联网安全中心)。你可以在 Github 上找到这个项目:【https://github.com/docker/docker-bench-security[。Dockerbench 正在检查与操作系统配置和安全性相关的常见最佳实践。(iptalbes、分区、目录权限等。)另一边是 Kubenech，它正在对 kubernetes 的实现进行基准测试。Kubebench 在 Github 上太:](https://github.com/docker/docker-bench-security)[https://github.com/aquasecurity/kube-bench](https://github.com/aquasecurity/kube-bench)。测试是用名为 YAML 的非常棒的编码语言编写的。整个框架由 GO 构建。对于围棋爱好者来说，完美的新闻和挑战开始有所贡献。所有的格子都受到 CIS kubernetes benchmark 的启发。

## MobSF

这是我最喜欢的网络动物。如果你是 pentester，architect 或者 mail，如果你是 HACKITECT，那么开始让你的思维变得敏锐。完美安装是在 ubuntu 与 docker。这个针对移动应用的测试框架甚至被多家咨询公司使用(从他们的报告中很容易阅读)。在几秒钟内，你得到反编译的应用程序，源代码审查和建议做什么。

真正好的功能是动态分析，适合喜欢探索的人。这个*框架支持 iOS (ipa)、Android (apk)和 Windows phone 应用(appx)* 。如果你没有开始安装，你还有机会。

> MobSF 旨在使您的 CI/CD 或 DevSecOps 管道无缝集成。MobSF 甚至有 Web API。你还能希望什么。

## 统治者

好了，好了，好了，现在我们开始认真了。你知道区块链吗？是的。这是 2018 年又酷又炫的流行语。这个星球上有人想让它真正有用。Sovrin 是基于 DID(分布式标识符)数据模型和语法的公共区块链身份实现，此处提供:[https://w3c-ccg.github.io/did-spec/](https://w3c-ccg.github.io/did-spec/)。Sovrin 由开源的 Hyperledger Indy 构建。自主权身份可能是未来的分散式活动目录，如提供安全和不可变身份和身份管理功能的系统。更多的细节是单独的文章。

最后一个更哲学的问题是，你认为像 AD 这样的恐龙技术会在数字转换时代，自我主权身份的时代幸存下来吗？

## Hashicop 金库

作为代码的基础设施是当今不能被遗忘的话题。如果你知道 Hasicorp 的 Terrafom，你也会知道 Vault。所有敏感凭据、令牌和密钥材料的安全存储。

Vault project 不仅提供存储，还提供 AWS 凭证、密钥、SSH 凭证或 X.509 证书的注册。想要更多吗？下面是参考架构:[https://www . vault project . io/guides/operations/reference-architecture . html](https://www.vaultproject.io/guides/operations/reference-architecture.html)

## 检查

安全性和架构作为代码是当今的基本需求。不会再有空谈者，会有更多传递价值的人。作为一种代码，Inspec 是一种法规遵循工具，也可以称为审计工具。更多指南和信息可在[https://www.inspec.io/](https://www.inspec.io/)获得。这个框架是厨师社区做的。所以谢谢大家！受支持的平台包括 AWS 和 Azure 等云环境，甚至是容器和基础设施。结果可以导出为 JUnit 格式，供您的持续集成/开发使用。对于壳牌的乐趣，你可以玩检查壳牌。

我们知道，审计和安全正在向新标准发展。我对此感到高兴。

## 声纳曲

寻找源代码审查的解决方案。Sonarcube 不是最好的，但它会帮助你深入了解你的代码。Sonarcube 可以与企业 CI/CD 解决方案集成(Jenkins、Azure DevOps、Team city 等)。Socarcube 的人把自己描述为连续检查。声纳不是针对安全问题的(只是一部分)。所以不要指望 Fortify 或 Checkmarx 这样的功能。很棒的东西是有几个开源插件:[https://redirect.sonarsource.com/doc/plugin-library.html](https://redirect.sonarsource.com/doc/plugin-library.html)。该工具支持多种语言。

完整名单列在这里:[https://www.sonarqube.org/features/multi-languages/](https://www.sonarqube.org/features/multi-languages/)。

## 保安 _ 猴子

大公司也在与造粒机合作。网飞就是典型的例子。

安全猴子监测您的 AWS 和 GCP 帐户的政策变化和不安全的配置警报。最大的优势是 security monkey 可用于多云解决方案。我期待 AWS 安全检查自动化已经很久了。如果你正在创建基于 lambda 的安全，或者更好地说是事件驱动的安全，你必须深入搜索 github 以获得你的 aswers 或者坐下来开始编码。(上帝保佑你)。网飞的声明是:“支持 OpenStack 公共云和私有云。Security Monkey 还可以观察和监控您的 GitHub 组织、团队和存储库。”

## 安全密码箱

随着今天的自动化，公司正试图改善他们的 CI / CD。AWS(代码提交，代码部署)和 Azure (Azure DevOps)工具极大地帮助了这一点。当我们希望将 SSDLC 安全集成到项目的生命周期中时，我们需要自动化安全检查。一个优秀的工具是一个安全的代码盒，它集成了 nmap、nmap、sslyze、wpscan、ZAP proxy 等小工具。

源代码可在
[https://github.com/secureCodeBox/secureCodeBox](https://github.com/secureCodeBox/secureCodeBox)获得。

整个系统基于微服务架构。每个现代敏捷黑客都会为 DevSecOps / SecDevOps 选择类似的微服务架构。

这篇文章是活的生物，所以我会编辑，添加和改进它，使一切准确无误。

**尊贵的门蒂诺斯(不是真正的动物园):**

*   OWASP ZAP 代理
*   OpenVAS
*   灰色日志
*   麋鹿栈(Elastic serach，kibana，logstash)
*   蜘蛛扫描仪