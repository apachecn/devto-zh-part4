# 你提交的被拒绝的会议建议的标题和/或摘要是什么？

> 原文：<https://dev.to/mikeysanchez/what-s-the-title-and-or-abstract-of-a-conference-session-proposal-you-submitted-that-got-turned-down-4l4n>

我曾有幸在甲骨文大会上发言，但首先获得批准并不容易。我被拒绝的次数比被批准的次数还多。

所以本着分享失望的精神，我先来。这是我提交给 IOUG 合作会议的一份会议提案:

### TITLE

超越回溯:使用 UTL 调用堆栈实现简单的 PL/SQL 插装

### 摘要

有几个开源库可供 PL/SQL 开发人员轻松地在 Oracle 数据库中检测他们的代码。像 ILO、Logger 和 BMC_DEBUG 这样的框架是健壮而稳定的产品，可以帮助开发人员开始使用工具。这些库中的一个共同缺点是它们需要支持 Oracle 的旧版本，因此不能利用新的(在 Oracle 12.1 中)包 UTL _ 调用 _ 堆栈。

UTL _ 调用 _ 堆栈包提供了对 Oracle 堆栈的访问，PL/SQL 开发人员必须事先想出巧妙的变通办法才能访问该堆栈。这种新的 API 允许开发人员很容易地知道他们在调用堆栈中的确切位置，从而提供了一种方法来将插装代码与对 DBMS_APPLICATION_INFO 和 DBMS_MONITOR 或 DBMS_SYSTEM 的调用(如果需要)包装在一起。

在本专题讲座中，Mikey Sanchez 将回顾在 Oracle 数据库中实施 PL/SQL 规范的优势和方法。将介绍一个利用 UTL 调用堆栈的简单框架，以简化现有 PL/SQL 代码的测试。他还将提供关于如何使用 UTL 调用堆栈包的提示，以便在异常期间提供比简单回溯更多的洞察力。