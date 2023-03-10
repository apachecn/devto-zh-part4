# 3 个假想的安全攻击和讨论

> 原文：<https://dev.to/rpalo/3-hypothetical-security-hacks-and-discussion-2b1f>

对于我所在的安全基础课程，一个小作业是提出三个假设的黑客攻击，并讨论它们可能如何发生，以及如何预防或减轻它们。当我完成时，我对它们的结果非常满意，所以我想在这里分享一下。它们并不全面，因为这是一个非常小的作业，但是我认为有一些很好的讨论点。

希望你喜欢！

* * *

## 场景 1:该死的，杰瑞！

攻击者是 Jerry，一名因个人卫生问题和懒惰而被解雇的心怀不满的员工。在被解雇之前，他曾与 SecureURStuff 公司有关联。现在他只能靠自己了。

他被解雇了，他认为这是错误的解雇。他坚持说，在他没有按时完成任何任务的几个月里，他只是“经历了一些事情”。

在他被解雇一周后，他意识到他的登录凭证仍然适用于 VPN 和主业务服务器。他远程登录删除了一堆非常重要的文件。他还把网站更新为“杰瑞·鲁莱兹”

通过在终止时实施用户帐户策略，备份他的数据并撤销他的所有访问权，可以防止这种攻击。此外，他可能不需要访问重要的业务文档和实时网站文件。他们可以通过遵循最小权限原则来减轻他的影响，只给他工作所需的权限。最后，他们真的应该备份他们的服务器、所有重要的文件和他们的网站，以便他们可以快速回滚和检索被删除/损坏的文件。

## 场景二:高速公路到...

攻击者只知道他们的黑客别名“ [D@ngerZ0NE](mailto:D@ngerZ0NE) ”D @ ngerZ0NE 与包括 Anonymous 在内的几个关注隐私的黑客组织合作。他们认为，包括亚马逊在内的大型科技公司太普遍了，在一个地方获取了我们太多的数据。

为了严重削弱亚马逊的运输基础设施，他们侵入了联邦航空局的交通控制数据库，扰乱了一切。为了避免任何损失或死亡，联邦航空局立即停飞了所有飞机，给航空公司、企业和其他人造成了巨额损失。但是没有人受伤。

不幸的是，联邦航空局局长使用了他的狗的名字(Shmoopsie)作为他的主密码，而且，因为他的脸书时间线实际上一直都是 Shmoopsie，所以 D @ ngerZ0NE 能够猜出他的密码。

主要的解决办法是实施安全的密码策略，尤其是对于那些能够访问非常敏感的数据的人。尤其是，尤其是如果数据是超级敏感和潜在的生命危险。强制密码最小长度、符号、大小写字母和数字，以及每 90 天或更长时间未使用的新密码。最好使用双因素或更好的认证。甚至可能是生物特征部件。

## 场景三:理工妮娜

袭击者是一个名叫尼娜的 12 岁男孩。她在六年级，但除此之外没有黑客组织。她对计算机超级感兴趣，当她注意到学校网站的一个漏洞时，她跟着一些教程，因为她想看看她是否能做到。

当她注意到她的学校网站有一个最大密码长度——这是他们以纯文本形式存储密码的明确标志——她想知道还有多少其他漏洞。事实证明，他们在登录页面上有一个 SQL 注入漏洞，允许她转储整个用户表。他们使用的是 WordPress 的过时版本。当她告诉她的校长时，他不相信她。所以她给他们写了封信，然后又和他见了一次面，讨论帮他们修好它能得到多少额外的学分。

他们需要更新 WordPress 和任何其他库的软件版本，散列和加盐他们的密码以保护他们的用户以防更多的 Ninas，并确保他们在使用表单输入查询数据库之前净化表单输入以避免 SQL 注入。更重要的是，他们需要认真对待所有安全报告和披露，并全面调查向他们报告的任何事情。他们需要重新审视自己的个人偏见。干得好，尼娜！