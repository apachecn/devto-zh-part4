# 关于渗透测试:标准和指南

> 原文：<https://dev.to/sm0k3/about-penetration-testing-standards-and-guides-pmn>

[![Outsourcing](img/a3f5248fc27330316e70f71d2c035aa3.png "About penetration testing: Guides & Standards")](https://res.cloudinary.com/practicaldev/image/fetch/s--JthMTrZj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ka3j5hgohajkbmqbie4s.jpg)

## 这是干什么用的？

每个人都知道渗透测试过程是某种“艺术”。它需要各种技术、操作系统等知识，以及对事物工作原理的理解。此外，你应该关注细节，并对任何弱点如何被利用有丰富的想象力，无论是系统还是人。

Pentesters 是独特的人，他们有自己的工作风格，但在企业世界中，即使考虑到商业基础，我们也应该遵循某种标准，以确保工作范围将被完全覆盖，我们需要以某种方式对漏洞进行分类，是的，这里有常用的标准、指南和分类。

此外，有时客户甚至会询问您的计划和技能，您需要描述工作过程(渗透测试)。

## 渗透测试的流行标准和指南

我想到的最流行的标准是:

*   OWASP 测试指南-web 应用安全测试的流行标准和指南，涵盖了针对 web 应用及其逻辑的大量安全检查。
*   [OWASP 移动测试指南](https://www.owasp.org/index.php/OWASP_Mobile_Security_Testing_Guide) -是一份针对 iOS 和 Android 移动安全测试人员的全面的移动应用安全测试和逆向工程手册，涵盖了从测试环境部署到安全测试执行过程中可以使用的工具和技术的所有内容。
*   [WASC](http://projects.webappsec.org/f/WASC-TC-v2_0.pdf)——web 应用安全测试的旧的和扩展的指南。WASC 包括大量的安全检查，安全专家可以针对 web 应用程序执行这些检查。官方的说法听起来像是:“WASC 威胁分类是一项合作努力，旨在澄清和组织对网站安全的威胁。”
*   [NIST 800-115](https://csrc.nist.gov/publications/detail/sp/800-115/final)——网络和基础设施安全评估非常流行的常用标准(包括 GOV)。
*   渗透测试框架——关于如何执行[渗透测试](https://dev.to/sm0k3/about-penetration-testing-143l)的简单逐步指南(比如从零到英雄)。

## 我们为什么要使用这些指南

首先，这种指南的使用不仅有助于正确地执行测试，而且保证通过遵循来自例如 OWASP 测试指南或 NIST 的指令来完成某个最少的检查。所有这些标准都是基于以前犯过的错误和以前的经验，以及当前和过去的统计数据和最佳做法。

让我们强调一些要点:

1.  不仅从执行者的角度，而且从潜在客户的角度理解 pentest 是如何进行的(对我们来说问题较少)。
2.  我们必须从一开始就把事情做好。因此，我们可以依靠公认的标准。
3.  我们必须进行某种基本的最低限度的检查，你不会把所有的事情都记在脑子里，这里标准再次帮助了我们。超越-的一切都将基于你的想象和经验。
4.  所有符合性检查都基于此类标准。
5.  如果你不知道什么，你总是可以参考标准。尤其是当您刚刚开始从事网络安全工作时，或者例如您需要请求渗透测试，但不知道要问什么问题、如何创建工作范围、如何理解雇佣公司的专家是否正确执行检查时，这非常有用。

## 漏洞分类和适当的风险评估

漏洞也可以借助标准和分类进行分类。让我们来看看一些流行的分类:

*   [OWASP TOP10](https://www.owasp.org/index.php/Top_10-2017_Top_10) -用于网络应用。
*   [OWASP TOP10 Mobile](https://www.owasp.org/index.php/Mobile_Top_10_2016-Top_10) -用于移动应用。
*   CVSS -通用漏洞评分系统可用于其他任何事情。

当您需要创建报告并描述漏洞时，向技术负责人解释这个或那个漏洞是关键的，您如何做到这一点？

所有已识别的漏洞(或安全错误配置)都应该以某种方式用以下参数进行分类:

*   严重
*   可能性
*   影响(业务)级别

根据从您的扫描和/或手动研究中获得的结果，您可以稍后将它们与来自 OWASP 或 CVSS 的信息相关联，并为每个漏洞指定特定的严重级别(即:低、中、高)。