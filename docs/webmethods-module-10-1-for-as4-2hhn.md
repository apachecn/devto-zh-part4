# AS4 的 webMethods 模块 10.1

> 原文：<https://dev.to/techcommunity/webmethods-module-10-1-for-as4-2hhn>

# 发现新的特性和功能

Software AG 的[在混合集成领域的领导地位](https://www.softwareag.com/info/integration/forrester-wave-2019-strategic-ipaas-and-hybrid-integration-platforms/default.html?utm_source=web_awards&utm_medium=web&utm_campaign=multi_ipaas)在很大程度上是基于 300 多种免编码、即用型适配器的可用性。

这些连接器的子集专用于促进 B2B 交易(EDI、EDIINT 和行业协议)，与 Software AG 的 B2B 集成平台 webMethods Trading Networks 协同工作:

| 企业对企业 |
| --- |
| 1 同步 |
| 自动化交易所 |
| AS4 |
| 化学标准 |
| ebXML |
| EDI(ANSI X12、UN/EDIFACT、VICS、UCS、ODETTE、Tradacoms、EANCOM、VDA) |
| EDIINT(AS1、AS2、AS3) |
| 固定 |
| HIPAA |
| HL7 |
| ISO 8583 |
| papiNet |
| 玫瑰网 |
| 迅速的 |

[![](img/19396a8751f03528e7ec003eeabdc177.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8p-HjuoQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10630400/webMethods_AS4_1.png/e3d85857-cf6f-491e-b6c0-4d6b1b16d7a2%3Ft%3D1563356334139)

## 关于 AS4 连通性的说明

自 2002 年以来，AS4 作为一个简化和标准化 B2B 数据交换和集成的 web 服务使用的互操作性协议，慢慢地获得了采用(主要是在欧洲)。它有助于在交易网络中安全地交换与有效负载类型无关的消息。

AS4 的 webMethods 模块作为在 ebXML 消息传递服务(ebMS)规范的 AS4 概要中指定的 AS4 ebHandler 一致性概要的更精简和简化的实现而广受欢迎。

AS4 的 webMethods 模块可以支持任何 B2B 文档、格式和传输，标准化和简化日常业务交易，并大大减少手动和基于纸张的流程，从而带来真正的业务优势。

## 新增:支持 ENTSOG 的 AS4 web methods 模块 10.1

通过最新发布的 AS4 模块 10.1，webMethods 扩展了我们的 AS4 功能，提供了 AS4 ebHandler 一致性配置文件的实现，该配置文件得到了 [ENTSOG](http://www.entsog.com/) (欧洲天然气传输系统运营商网络)的支持。

ENTSOG 的既定使命是“促进天然气内部市场的完善和刺激跨境贸易”，它认为自己的角色是市场促进者，加强“欧洲各国天然气输送系统运营商(tso)之间的信息互通和标准化，以确保泛欧输送系统的发展符合欧盟能源目标”。

10.1 的 ENTSOG 增强的 AS4 功能的部分列表包括:

*   支持双向/推-推 MEP:支持使用双向/推-推与贸易伙伴异步发送和接收用户消息。
*   AS4 ENTSOG 协议和协议更新:支持 AS4 ENTSOG 协议和协议更新规范，以便与 ENTOSG AS4 消息灵活地交换证书。
*   TPA 级别的合作伙伴证书配置:提供配置合作伙伴证书的能力，以便在贸易伙伴协议(TPA)级别对用户消息进行签名、验证、加密和解密。
*   从 ebMS 3.0 用户消息中提取附件:支持作为单独的事务从 ebMS 3.0 用户消息中提取附件。

本质上，AS4 10.1 中的 ENTSOG 更新代表了 webMethods 连接器库的一个额外的行业标准，其中还包括 HIPAA、HL7、papiNet、RosettaNet 和 SWIFT。

要查看其他 webMethods B2B 标准，请[点击此处](https://resources.softwareag.com/b2b-integration/2019-6-fs-webmethods-estandards-en-get-the-facts-about-webmethods-estandards)。