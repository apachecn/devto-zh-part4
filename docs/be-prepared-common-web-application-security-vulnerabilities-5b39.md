# 做好准备！常见的 Web 应用程序安全漏洞

> 原文：<https://dev.to/rebeccaberis_24/be-prepared-common-web-application-security-vulnerabilities-5b39>

[![](img/c0f72416bc17c3c0d09d6fc57e8b7082.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TX23RoLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b491meo7gi62qfgoc7v6.jpg)

<figure>

<figcaption>

<figure>[Pixabay](https://pixabay.com/illustrations/internet-computer-screen-monitor-1593448/)

<figcaption></figcaption>

</figure>

</figcaption>

</figure>

在云中运营的企业数量每天都在增长。消费者对全天候访问任何数据的需求促使组织通过 web 应用程序、在线银行和电子商店提供这些数据。攻击者在这个漏洞中找到了获取和恶意使用敏感信息(如信用卡数据)的新机会。

希望提高网站和应用程序安全性的公司会应用 web 应用程序安全实践和工具。在本文中，您将了解什么是 web 应用程序安全性，什么是最常见的 web 应用程序漏洞，以及保护您的 web 应用程序的最佳实践。

## 什么是 Web 应用安全？

[web 应用安全](https://searchsoftwarequality.techtarget.com/definition/application-security)是信息安全的一个分支，专门研究网站、Web 应用和 Web 服务的安全。鉴于对安全解决方案和资源的需求日益增长，开发人员和组织采取了一些措施。

开放 web 安全项目(OWASP)成立于 2001 年，是一个非营利性组织，目标是提高软件和 Web 应用程序的安全性。该组织从多个安全组织收集信息，并编制了一份名为 OWASP 十大漏洞的顶级 web 安全漏洞列表。他们发布了 [OWASP 依赖检查](https://resources.whitesourcesoftware.com/home/owasp-dependency-check)，这是一个免费的软件组合分析工具，可以识别项目依赖并检查它们是否有任何已知的漏洞。

## 最常见的 Web App 安全漏洞

Web 应用程序漏洞可能是由人为错误或应用不当的安全措施(如缺乏适当的输入/输出保护)造成的。攻击者可以使用一系列方法来操纵数据库或破坏整个网络。OWASP 根据以下因素对漏洞进行优先级排序:

*   **可利用性**—漏洞的可利用性如何？当攻击者可以用不太复杂的编程和工具来破坏系统时，我们称之为高度可利用性。
*   **可检测性**—定义检测威胁的难易程度。
*   **影响**—如果漏洞被利用，可能造成的损失量。攻击者可以使用不同的方法来利用漏洞，包括:
*   **SQL 注入**—攻击者可以通过让 web 应用程序执行数据库 SQL 命令来暴露后端数据库。您可以通过避免详细的错误消息来防止这种情况，这些错误消息对攻击者非常有用。
*   **跨站点脚本**—也称为 XSS。攻击者使用 XSS 在受害者的浏览器上执行脚本。当应用程序可以获取不可信的数据并将其发送到 web 浏览器时，攻击者就有可能劫持会话 cookies、篡改网站或在受害者的机器上运行恶意软件。可以预防
*   **身份验证和会话管理中断**—当用户结束会话且 cookies 未失效时，敏感数据仍保留在系统中。攻击者可以使用相同的系统，通过窃取敏感数据打开以前的会话。通过根据 OWASP 应用程序安全验证标准定义身份验证和会话管理要求，可以防止这些威胁。
*   **不安全的直接对象引用**—攻击者使用对内部实现对象(例如，文件或数据库密钥)的公开引用来访问其他对象，作为到达未授权数据的桥梁。您可以通过实现访问控制检查来避免这种违规。
*   **安全错误配置**—当没有为网络的所有部分定义和部署安全配置时，攻击者可以访问敏感数据并危及数据的机密性或系统的功能。维护强大的应用程序架构和更新的软件有助于保护系统。另一个好的实践是禁用目录列表和实现访问控制检查。
*   **拒绝服务(DoS)和分布式拒绝服务(DDoS)攻击**—攻击者用大量流量使目标服务器过载，使带宽饱和。因此，服务器不能有效地处理传入的请求，减慢了进程并最终拒绝对传入请求的服务。这可以通过测试反自动化、帐户锁定、HTTP 协议 DoS 和 DDoS 攻击来防止。
*   **缓冲区溢出**—攻击者向缓冲区写入恶意代码，意图超出其边界，并导致相邻的内存位置被数据覆盖。这可能导致内存故障或崩溃。攻击者还可以将恶意代码注入目标机器的内存。##如何保护我的 Web 应用程序？有几种最佳实践和工具可以帮助防范应用层攻击。一种这样的方法是以网络应用防火墙(WAF)的形式设置一个屏障。这种防火墙可以抵御跨站点伪造、跨站点脚本和 SQL 注入等攻击。

[![](img/b30a64ec2cf58a35317dd6b5a1ec56bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B2iIHZjN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bbw1m6x6w88f9tdh8bml.png) 

<figure>

<figcaption>

<figure>[图来自维基媒体](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/WAF_Archi.png/800px-WAF_Archi.png)

<figcaption></figcaption>

</figure>

</figcaption>

</figure>

保护您的 web 应用程序的其他实践包括:

*   **随时更新 OWASP 十大**—定期查看 OWASP [Web 应用程序安全测试备忘单](https://www.owasp.org/index.php/Web_Application_Security_Testing_Cheat_Sheet)，了解有关新的已知漏洞和其他安全相关问题的信息。
*   **审核您的应用程序安全性**—这将创建一个增长的基准。进行外部应用程序安全审计以获得关于应用程序安全性的独立观点是很重要的。这可以通过根据审计结果重构代码，帮助您从一开始就构建安全的应用程序。
*   **正确记录您的活动**—当威胁发生时，手头有信息。这可以通过实现工具来检测您的应用程序，并以在需要时可以快速有效地解析的方式存储信息来实现。
*   **使用实时安全监控**—添加运行时应用程序自我保护(RASP)工具或使用[应用程序安全管理](https://www.ibm.com/support/knowledgecenter/en/SSW2NF_9.0.0/com.ibm.ase.help.doc/topics/c_applications.html)平台来保护您的应用程序免受内部和外部威胁。
*   **加密一切**—从各个角度考虑加密非常重要，包括静态数据，而不仅仅是传输中的数据。这样，如果有人可以进入您的服务器并克隆或移除驱动器，无论他们是内部还是外部威胁，加密都会使攻击者的工作更加困难。
*   **更新您的硬件和软件**—根据最新的安全版本更新您的资源是一种久经考验的防止安全问题的方法。有一些工具可以帮助您自动搜索和实施安全更新。

## 底线

通过遵循这些最佳实践，例如安装安全补丁、安装实时监控服务器的工具以及使用安全工具，您可以走上保护应用程序的正确道路。此外，通过掌握最新的已知漏洞，您从一开始就为构建安全的应用程序做出了贡献。这将最终增强您的安全态势，并使您的用户受益。