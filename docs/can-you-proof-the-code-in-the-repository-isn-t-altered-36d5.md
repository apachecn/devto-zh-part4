# 你能证明存储库中的代码没有被修改吗？

> 原文：<https://dev.to/arjenpostma/can-you-proof-the-code-in-the-repository-isn-t-altered-36d5>

在开发开源软件时，如何向用户证明运行的代码实际上与存储库中的代码相同？

如果我开发一个处理敏感数据的开源网站，理论上用户可以检查源代码，看看他们的数据是否没有发生任何奇怪的事情，比如将用户信息发送给第三方服务。但是，在部署到服务器之前，如何证明存储库中的代码没有在其他地方被编辑呢？

或者一个更直接的例子。Ben 如何证明这个网站实际上使用了

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ forem ](https://github.com/forem) / [ forem](https://github.com/forem/forem)

### 为社区赋权🌱

<article class="markdown-body entry-content container-lg" itemprop="text">

# Forem <g-emoji class="g-emoji" alias="seedling" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f331.png">🌱</g-emoji>

**For Empowering Community**

[![Build Status](img/a59640885320d80449880ba412cd1540.png)](https://app.travis-ci.com/github/forem/forem)[![GitHub commit activity](img/c636d7221527a9f58a1bcf7eb4f234ec.png)](https://camo.githubusercontent.com/64b9f7c7c5f41ec22113b61235256435cd61779a0554b0595b88b6011f94c60b/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f636f6d6d69742d61637469766974792f772f666f72656d2f666f72656d)[![GitHub issues ready for dev](img/a2f086092c44aa16ddbf13a9cf99fea1.png)](https://github.com/forem/forem/issues?q=is%3Aissue+is%3Aopen+label%3A%22ready+for+dev%22)[![GitPod badge](img/e87aed75ceeae18e025435d5084e1209.png)T11】](https://gitpod.io/#https://github.com/forem/forem)

欢迎来到 [Forem](https://forem.com) 代码库，这个平台为[开发到](https://dev.to)提供动力。我们很高兴你能来。在你们的帮助下，我们可以增强 Forem 的可用性、可伸缩性和稳定性，从而更好地为我们的社区服务。

## 什么是 Forem？

Forem 是用于构建社区的开源软件。为你的同行、客户、粉丝、家人、朋友以及任何其他需要聚集在一起成为集体一员的时间和空间的社区[参见我们的公告帖子](https://dev.to/devteam/for-empowering-community-2k6h)以获得 Forem 是什么的高级概述。

[dev.to](https://dev.to) (或者只是 dev)由 Forem 托管。这是一个软件开发人员的社区，他们撰写文章，参与讨论，并建立自己的专业档案。我们重视支持性和建设性的对话，为所有成员追求伟大的准则和职业发展。该生态系统涵盖从初学者到高级开发人员，欢迎所有人找到自己的位置…

</article>

[View on GitHub](https://github.com/forem/forem)

也许我在看一些非常明显的东西。我知道在下载文件时，你可以使用校验和来确认它们是相同的，但是你能把同样的技术应用到一个网站上吗？您可以将从存储库中生成的校验和放在网站的底部。但这并不能证明什么。

你想过这个吗？对此有经验吗？说来听听。