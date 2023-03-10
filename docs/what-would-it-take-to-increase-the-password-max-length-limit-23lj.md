# 如何提高密码最大长度限制？

> 原文：<https://dev.to/mitchpommers/what-would-it-take-to-increase-the-password-max-length-limit-23lj>

在上一篇文章中，我们确定了[密码最大长度限制是好的](https://dev.to/mitchpommers/password-max-length-limits-are-dumb-but-we-need-them-1dpp)，如果它们被设置在一个合理的限制。它们支持测试，以确保密码设置不会超过您的系统(基础设施、库、哈希算法和您自己的代码)允许的长度。让我们看看一些澳大利亚银行使用的限制，并确定采取什么步骤使它们符合 [OWASP 对密码长度](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)的建议。

首先，我们需要知道当前的规则和限制是什么。我找到了 8 家银行的长度限制，并总结了以下信息。

# 密码长度限制

| 银行 | 输入字段限制 | 密码限制 |
| --- | --- | --- |
| ANZ | Sixteen | Sixteen |
| 西方银行 | Sixteen | Sixteen |
| 本迪戈银行 | eight | eight |
| 工程量清单 | Two hundred and fifty-five | Twenty |
| 英联邦银行 | Sixteen | Sixteen |
| 麦夸里 | 无限的 | Two hundred and fifty-six |
| Suncorp | 无限的 | eight |
| 西太平洋银行 | six | six |

注意:NABs [密码提示页面](https://www.nab.com.au/about-us/security/cyber-safety-tips-for-you/six-simple-ways-to-protect-your-passwords)允许设置 17 个字符的密码。由于我无法找到他们的密码规则，我已经把他们从这个列表中删除了。

## 分组

我们可以把银行分成几组:

最大长度低且输入长度有限的:

*   西方银行
*   本迪戈银行
*   英联邦银行
*   西太平洋银行

最大长度较低而输入长度较高的:

*   ANZ
*   单身军官宿舍(bachelor officers' quarters)
*   Suncorp

已经处于有利地位的麦格理银行

# 从低到 Woah！

为了增加长度限制，我们需要做一些改变。我们这样做的假设是:

*   密码目前以哈希方式存储
*   您的密码不会发送到古老的主机(更改率低，更改成本高)
*   不存在阻止更高密码限制的技术限制
*   只有一个系统使用密码

## 低最大长度和有限输入长度

朝着更好的状态努力的第一步是增加登录时可以输入的密码长度。如果我们从设置密码开始，然后改变屏幕，那么用户可以创建比他们登录时间更长的密码。这将导致问题。增加登录屏幕限制也会导致问题。

以密码为例:“银行密码 123456”(18 个字符)

假设这是我的网上银行密码。

这是我注册时输入的内容，也是我每次登录时输入的内容。

但是我的银行只有 16 个字符的密码限制。

当我第一次设置密码时，我在键盘上输入了这 18 个字符。字段长度限制阻止了最后两个字符的输入。然后，我的 18 个字符的密码中只有 16 个字符被发送到服务器。他们的登录界面也把我限制在 16 个字符以内。我每次输入 18 个字符，但登录时没问题。对我来说，18 个字符是正确的，16 个字符是错误的。

> 我输入的内容:bankpassword123456
> 我看到的内容:***************
> 服务器看到的内容:bankpassword1234

那么，当用户输入服务器看不到的字符时，如何增加密码字段的长度呢？密码不以明文形式存储，因此您无法提前识别受影响的用户。你需要确定一个处理这种情况的计划。

你可能认为这是一个边缘案例。我看到过对密码长度限制的取消处理不当的情况。它引起了混乱。用户无法登录，他们也不知道为什么。试图登录的用户给支持热线打了数百个电话。最后，我们不得不恢复限制，以恢复一些秩序。

您的选择包括:

*   尝试与每一位顾客沟通
*   花时间确定哪些客户会受到影响
*   做好准备，以防被锁定的用户来电增多

一旦你选择了你的选项并花时间计划，那么你就可以开始执行它了。

这是大量的工作，没有用户会注意到的新功能、增强功能(除非它们是技术性的)或显著的安全优势([鉴于银行已经在密码安全上做了什么](https://www.troyhunt.com/banks-arbitrary-password-restrictions-and-why-they-dont-matter/))。银行是收入驱动的企业。做出改变需要法律义务或超过成本的利益。增加密码长度在目前是不可能的，所以这些第一组不太可能做出改变。

## 低最大长度和高输入长度

任何通过第一道障碍的银行都将进入下一组的更容易的领域。用户输入的内容会得到验证。任何密码为“银行密码 1234”的人，如果输入“银行密码 123456”，就已经无法登录。要使密码更长，您需要增加密码更改、设置和登录字段的限制。您还需要确保不会回到第一组，在第一组中，最大输入长度等于最大密码长度。

就是这样。你完了。你已经做出了改变。开始测试并证明在您的网络中没有一个安全设备需要短密码。

# 结论

在更改密码规则之前，需要考虑很多事情。对它们进行更改不像增加用户名或名称字段的长度那么简单。您需要注意的是，您的用户不会计算密码字段中出现了多少个字符。你需要了解每一个看到你的用户密码的系统。每个拥有自己登录界面的系统都需要一起经历这些变化。只有当它们都准备好了，你才能增加你允许用户输入的最大长度。

下一次将是关于集中你的密码屏幕或者为什么密码规则如此糟糕。我还没决定哪一个。