# 如何在 Windows 服务器中寻找可疑活动

> 原文：<https://dev.to/xpolog/how-to-look-for-suspicious-activities-in-windows-servers-52i5>

您正在运行一个包含许多 Windows 服务器的大型生产环境。
网络中有多个林，有些林有多个域控制器。

您的 Windows 服务器安全至关重要–您希望跟踪和审核可疑活动，并查看从 Windows 服务器事件日志中提取的详细 Windows 报告。

阅读文章，此处有图片和设计>[https://www . xplg . com/windows-servers-security-suspective-activities/](https://www.xplg.com/windows-servers-security-suspicious-activities/)

在 Windows 中查找可疑活动非常重要，原因有很多:

*   Windows 比 Linux 有更多的病毒和恶意软件。
*   人们经常在断开连接时让他们的远程桌面会话保持运行，使这些会话成为未经授权的接管的主要目标。
*   服务帐户经常使域管理员回避访问问题。
*   服务帐户的已知密码成为黑客的公开后门。
*   为了获得可接受的应用程序性能，有时会禁用防病毒和本地防火墙。
*   修补周期被错过或有时被完全忽略，使得 Windows 系统容易受到潜在的攻击。

一句话:预防胜于治疗，这就是为什么要采取一切可能的安全措施。

免费下载 XpoLog 7，自动发现可疑事件！轰>[http://bit.ly/2XGJOJV](http://bit.ly/2XGJOJV)，

Windows Server 报告
应该有一个可靠的安全监控流程。

这种类型的监控会监视谁或什么在何时登录到 Windows 服务器，以及这些登录事件是否看起来可疑或不正常。

这不仅有助于及早发现潜在的威胁，还能在发生违规时提供线索。

Windows 报告–寻找什么？作为一名有安全意识的管理员，您需要密切关注许多事件，例如:

*   登录 Windows 网络、域控制器或成员服务器的成功或失败尝试。
*   成功或失败的远程桌面会话尝试。
*   重复登录尝试后密码锁定。
*   在营业时间之外成功或失败的登录尝试。
*   添加、删除或修改本地或域用户帐户或组。
*   将用户添加到特权本地或 active directory 组。
*   清除域控制器或成员服务器中的事件日志。
*   更改本地审计策略和组策略。
*   更改或禁用 Windows 防火墙或防火墙规则。
*   添加新服务、停止或删除现有服务。
*   更改注册表设置。
*   更改关键文件或目录。

在本教程中，我们将讨论在 Windows 服务器中启用一些重要的安全审计来帮助捕捉可能的威胁。

阅读本教程后:您将有足够的信息来提高您的 Windows 服务器的安全级别和工作站舰队，并保护他们免受恶意活动！

点击此处阅读全文>[https://www . xplg . com/windows-servers-security-suspective-activities/](https://www.xplg.com/windows-servers-security-suspicious-activities/)

42 要遵循的关键安全事件
有些关键安全相关事件应该包含在您的审计视图和常规搜索中。

我们在这个有用的“备忘单”中编辑了这些事件 id 及其描述的列表。你也可以从我们博客上的文章中获得这个列表。