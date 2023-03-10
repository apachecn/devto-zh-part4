# 聊天机器人安全框架:关于聊天机器人安全你需要知道的一切

> 原文：<https://dev.to/secxena/chatbot-security-framework-everything-you-need-to-know-about-chatbot-security-30nc>

一个周日的晚上，我正在黑我的聊天机器人，我突然意识到一个开发团队在开发这些功能时是多么的无知。将我的聊天机器人的安全性与流行的聊天机器人进行比较的想法引起了我的兴趣。所以我决定探索基于印度的聊天机器人产品的安全方面。在医疗保健和金融领域发挥作用。

我开始使用他们的聊天机器人，但他们都使用按钮和自定义用户界面等对话界面，而不是开放的键盘。获得用户恶意输入的机会几乎是不可能的，因此我已经逆转了应用程序，并获得了 API 服务器的详细信息。

从那里，事情迅速升级到一个不安全的服务器。我可以接触到公众可以接触到的。生产服务器上的 git/存储库。

获得 10 万多用户的医疗保健数据几乎花了 1 个小时。晚上 11 点我向那个组织举报了。他们在早上 6 点前更换了所有凭证，并 pinged 我进行验证。这是我有生以来见过的最快的安全措施。

[![Infra credentials dump of healthcare chatbot(left) - Portfolio dump wealth management chatbot(right)](img/bfa8d473225c5f8c7a6e4c679b02d4a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uHm0xIXz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/97bj4kph0ptbm6ydxn9u.jpg)

第二个聊天机器人我代理到我的打嗝套件，发现他们正在使用未签名的 S3 水桶 URI 的。令我惊讶的是，它有读/写公共权限。我浏览了水桶，直到我找到一个名为“投资组合报表”的文件夹。这是一个由所有投资细节组成的 1 万多个(确切地说是 11091 个)投资组合的转储。我发送了一封报告问题的电子邮件，但没有得到回复。

这件事之后，我开始考虑为聊天机器人兄弟会建立一个安全结构。我理解在严格的时间表下开发一些东西的痛苦，并且笔测试是一种爱好，我也知道看到琐碎错误的沮丧。过去，我曾多次问自己这个问题——我们如何既能在覆盖安全漏洞方面达到产品的质量，又能在新特性方面达到开发速度。

答案很简单——我们可以通过在开发的早期就开始良好的安全实践来做到这一点。但是，问题是当你开始你的开发项目时，在花费时间提高软件质量和集中精力开发更有价值的特性之间会出现一个普遍的争论。

通常，交付功能的压力主导了讨论，导致许多开发人员抱怨他们没有时间处理架构和代码质量。

这里的假设是，提高质量也会增加成本，但与直觉相反的现实是不同的。好的软件质量消除了减缓开发新功能的障碍，因此从长远来看降低了成本。

解决方法是时间分段。给开发人员足够的时间在开发前设计解决方案，在开发后设计安全场景。

[![](img/0f9b6973ee9802562d5e5eb10bb92cf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0lO9EIn4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dtonfqxmvshdpicipavk.png)

因此，如果我能够让你相信这很重要，那么我会给你一个坚实的框架来涵盖聊天机器人的安全。

我们应该从聊天机器人技术的出现开始思考，为什么我们使用它而不是主流的表单驱动应用程序。将聊天机器人与主流应用相比，有两个不同之处。首先，它模仿了人类的对话行为，提供了一种隐私感。当机器人询问工资细节时，如果有一个来回的对话，有助于设置背景和询问工资细节背后的推理，用户不会感到惊慌。其次，它提供了一种安全感，因为用户可以问任何问题，如果他们对机器人的答案有疑问。这种来回的交流是主流应用所没有的。

双向交流聊天机器人有助于打破用户之间普遍存在的不信任障碍。当私人问题在对话环境中被恰当地提出时，用户会发现更容易信任提问者。因此，聊天机器人会问一些问题，比如“你有什么严重的疾病吗？”、‘你的应急基金有多大？’，‘你的工资待遇如何？等等。

凭借这一权力，我们的主要责任是保护用户委托给我们的信息。此外，我们应该遵守我们行业的法规，并为了用户的利益而实施这些法规。最后，您必须保护您的基础架构。

我强烈建议您考虑所有用户的身份验证和权限——非客户、客户、内部用户，如果您正在某处记录所有系统活动。

这里我给出了一个基于问题的结构来决定你的聊天机器人是否安全。

## 特定域安全

让我们来看两个独立的使用案例:

首先，财富管理用例——如果用户想要管理他/她的财富，他/她需要公开他们一生的储蓄，以便进行风险分析。这些信息对你的用户很重要。例如，用户在机器人上输入工资详细信息，并设定在 2 年内将工资的 15%存起来买车的目标。

第二，医疗保健用例——如果用户想要跟踪他/她的健康需求，他/她需要公开他们的主要情况和正在发生的症状+药物治疗。这些信息是用户的重要私人信息。例如，一个用户说他/她有糖尿病，正在寻找解决问题的最佳方法。

先问这些问题。

1.  您知道客户提供的哪些信息属于个人身份信息(PII)或受保护健康信息(PHI)吗？
2.  在您的业务逻辑和数据存储中，PII-PHI 和非 PII-PHI 是否有明确的区分？
3.  您的客户的信息只有他们自己可以访问吗？。任何内部团队都可以访问它吗？
4.  有没有什么方法可以让某人进入一个不属于他的对话？
5.  你能区分你的客户是使用聊天机器人而不是其他人的账户吗？
6.  管理员在同一个机器人上有额外的特权吗？如果是的话，为什么？

这些问题是与领域无关的，为了设计一个安全的聊天机器人解决方案，你需要有这些问题的答案。

如果你问了这些问题，有足够的工具可以帮助你解决这些问题。我还设计了一个聊天机器人安全清单，可以帮助你解决这些问题。

> “没有需求或设计，编程就是给空文本文件添加 bug 的艺术。’——路易·斯利

## 技术堆栈

聊天机器人有三个基本组成部分，客户端平台、NLU 理工大学和后端基础设施。

从安全角度来看，这些事情都同等重要。

在这里，问这些问题，你会得到一个答案，如果你的实现是安全的。

1.  由于客户端应用程序在客户端机器上运行，它们能逆转它吗？如果能，它会不会暴露任何漏洞，如硬编码的密码或访问令牌、不安全的 API 或服务器访问？
2.  存储在应用程序本地存储中的应用程序的相关数据 PII 是否可以被其他应用程序访问，或者可以在不解密的情况下读取？
3.  一个用户可以在你的聊天机器人上手动操作每分钟最多 15 次(查询-响应),但是你允许每秒无限制的查询吗？如果是，那为什么？
4.  你的意图分类器对不同类别的用户 A 普通用户和 B 高级用户有不同的意图吗？来自 A 类的用户查询将触发 B 类意图的话语？
5.  如果一个用户不登录就可以使用你的聊天机器人，那么如果他/她询问你的聊天机器人，是否有任何意图不给予任何回答，并要求他登录？
6.  你有对话日志存储吗？您是否对日志中的 PII 进行了编辑/加密，或者取消了日志与其所有者的关联，以便内部团队中的任何人都无法访问它们？
7.  你确定你的后端开发和基础设施部署遵循了 chatbot 安全规范的最佳安全实践吗？你的系统就像它最薄弱的环节一样安全。

这些都是 beta 之前应该娱乐的问题。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [塞克西娜](https://github.com/secxena) / [聊天机器人安全清单](https://github.com/secxena/Chat-Bot-Security-Checklist)

### 聊天机器人安全清单

<article class="markdown-body entry-content container-lg" itemprop="text">

# 
[![Chat-Bot-Security-Checklist](img/12fe8b5795ced9f19d6979f7631543da.png)](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/data/cc.png) 

【聊天机器人安全清单】

#### 聊天机器人安全清单是一份详尽的清单，列出了在将聊天机器人投入生产之前你需要具备的所有要素。

[![PRs Welcome](img/d9e6de6a32f222f0ab85641af7d5ba5f.png)](http://makeapullrequest.com)[![Contributors](img/4fdb660a89665ad34c0513ec9ae5bbd9.png)](https://github.com)[![Chat‑bot_Checklist followed](img/1484934547d7198d359e9b361a7483d1.png)](https://github.com/)[![CC0](img/a3873220ac5a9f46360b4c825cc7942c.png)T11】](https://creativecommons.org/publicdomain/zero/1.0/)

[如何使用](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#how-to-use)[投稿](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#contributing)

它基于聊天机器人开发人员多年的经验，并添加了一些来自其他开源清单的内容。

## 目录

1.  **[客户端安全](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#Client-Security)**
    1.  [操作系统平台](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#OS-Platforms)
    2.  [消息平台](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#Messaging-platforms)
    3.  [定制网站平台](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#Custom-Web-Platform)
2.  **[NLP 技术安全](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#NLP-security)**
    1.  [意向分类器](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#Intent-Classifiers)
    2.  [对话管理系统](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#Dialog-Management-System)
    3.  [对话记录](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#Conversation-Records)
3.  **[后端+基础设施](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#Back-end-Infrastructure)**
    1.  [OWASP 前 10 名](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#OWASP-Top-10)
    2.  [发牌](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/#Licesnsing)

* * *

## 怎么用？

聊天机器人安全清单中的所有项目对于大多数项目来说都是必须的，只有出于监管原因才会有例外。你可以使用这个清单对你的聊天机器人产品实施三层防御。列表中的每个点都标记为低、中、高。

*   [![Low](img/76aeedab0e5cc945bb46957bbb4e66cb.png)](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/data/low.svg) 表示该项目是**推荐的**，但在某些特殊情况下可以省略。
*   [![Medium](img/5aaae54696cb292acd6e8f87848eea42.png)](https://raw.githubusercontent.com/secxena/Chat-Bot-Security-Checklist/master/data/medium.svg) 表示物品是**强烈推荐**并最终可以…

</article>

[View on GitHub](https://github.com/secxena/Chat-Bot-Security-Checklist)

测试版发布后，尝试做一个 VAPT 来保护遗漏的漏洞。然后，从长远来看，发布一个披露指南，并开始 bug bounty。

> “大多数人开始意识到，世界上只有两种不同类型的公司:被攻破并知道的公司和被攻破但不知道的公司。”

## 监管顾虑

当你居住在工业聊天机器人中非常常见的隐私敏感区域时，你需要有某个领域相关的认证，以便在该行业工作，再加上常见的政府强制规定。

认证有助于您实现管理机构要求的特定领域的安全性，并且是一种常见的行业惯例，但这并不意味着法规遵从性使您成为一个完全安全的组织。

有两种符合性

### 常见:

与行业无关的法规遵从性基于人口统计因素。

**ISO/IEC 27002** -主要关注公司财务、运营、物理安全和事故管理政策。
**【NIST】**——安全控制的分类、实施、监控和评估。
**-客户的个人数据只能保存到您的业务流程需要处理时。**

 **### 特定行业:

行业特定的法规适用于不同的行业和人口统计。

HIPPA -适用于医疗技术，主要关注 ePHI-电子保护的医疗信息安全。
**PCI-DSS** -主要用于支付，主要关注持卡人数据的安全存储、传输和执行。
**-这是针对教育技术行业的，主要关注学生教育记录的隐私。**

 **各种行业还有其他非技术认证，根据他们的人口统计，你也需要遵守这些认证。因此，在开发任何解决方案时，一个好的做法是考虑这三点。

1.  您已经为所有用户建立了身份验证机制。你必须知道谁在你的系统里。
2.  您有适当的授权系统——授予角色所需的最少权限。
3.  您有一个适当的监控系统来记录系统中发生的每一个活动，谁在做，他们是否有权限这样做。

这三点将帮助您实现一个健壮的安全系统，并且您不需要做很多工作来符合上述标准。

> “规章制度对于演习来说很好，但在危险时刻，它们就没有更多的用处了。你必须学会思考。”斐迪南·福煦

## 结论

聊天机器人系统拥有任何系统中常见的所有技术组件。所以不能再从轻处理了。你需要采取所有必要的安全措施来确保它的安全。当聊天机器人拥有由对话管理系统控制的对话式用户界面，并且意图分类器具有授权和安全的日志管理时，聊天机器人特定的技术是安全的。****