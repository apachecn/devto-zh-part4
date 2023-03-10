# 在我了解安全之前，我知道的关于安全的五件事

> 原文：<https://dev.to/hayleydenb/five-things-i-knew-about-security-before-i-knew-anything-about-security-mi9>

我是一家公司( [Snyk](https://www.snyk.io) )的开发者代言人，该公司专门帮助开发者发现并修复他们开源依赖中的安全漏洞。我的工作是与开发人员社区分享资源和见解，更重要的是，倾听开发人员的意见并获得他们需要的反馈。在此之前，我是一名网站开发人员，读过几次 [OWASP 十大](https://www.owasp.oimg/7/72/OWASP_Top_10-2017_%28en%29.pdf.pdf)。至少可以说，这是一个小小的变化。

老实说，我有时觉得自己像一个职业困惑者。我是安全领域的新手，我的许多同事对安全有着深刻的理解。但是，对于公司来说，拥有安全新手的视角是很有价值的，因为他们的大部分客户群也是如此。这没关系。我每周都在学习。

我有一个秘密，我想我应该分享它。

> 你对安全的了解比你想象的要多。

真的。

听一个比她六个月前知道得更多的人说。从一个还有很多东西要学的人那里学吧。人类*有一种安全思维*，即使他们没有写过一行代码。你在日常生活中所做的一些事情会让你对安全有所了解。

因此，在我对安全一无所知之前，我向你们展示了我所知道的关于安全的五件事:

## 事情 1:粒度权限很重要

我们都在社交生活中实践这一点。我告诉我的丈夫，或者我最好的朋友，或者我的妈妈一些我不会告诉我的同事，熟人，或者陌生人的事情。

当我读研究生的时候，我和我的室友都被允许在公寓的公共区域活动，但是我的个人房间有一把单独的锁。他们的也是。

当我参加一个会议时，我的徽章可以让我进入公共区域，但不一定是后台，或者是单独售票的教程。组织者的徽章可以让他们进入这些空间。

粒度权限是我们日常生活的一部分，通常由社会规范、有目的地指定的关系边界或某种物理安全(如上锁的公寓房间)来设置。它们可能是有目的地陈述的，但是你也可能只是假设了默认值。您可能没有意识到您在个人生活中授予了粒度权限，但是您绝对意识到了。

想想你生活中的这些粒度权限。为了更快乐、更健康的关系，要有目的性。并且在你创建的应用程序中要有明确的权限。问问你自己这些默认值是否有意义，并且记住授予一个新的权限总是比撤销一个权限更容易。

## 第二件事:你不必跑得过熊

有一个老笑话，讲的是两个徒步旅行者在旅途中遇到一只熊。他们开始逃跑。一个徒步旅行者对另一个说，“我们为什么要跑？我们跑不过一只熊！”第二个徒步旅行者回答说，“我不需要跑得过熊，我只需要跑得过你就行了”。

这话有很多道理。坏演员通常会选择容易得手的果实、机会犯罪或阻力最小的道路。领先于你的同行才能领先于熊。

## 事情三:汽车报警器没用

你调查过汽车报警器吗？我有，只有一次。我的邻居出城了，他的汽车警报器响了，并且持续响了 36 个小时。这引起了我的注意，但如果他的车真的被偷了，这还不够快。

人们不会被汽车报警器唤醒采取行动，因为它们的误报率高得离谱。对于每一个由真实犯罪引发的汽车警报，有数百个似乎是由风、一片落叶或一只看着它的狗引发的。

如果您收到 50 个安全漏洞的警告(这些漏洞最终并不是真正的漏洞)，您关注下一个警告的可能性有多大？

## 第四件事:双因素认证更安全

在我成为一个有钥匙的孩子之前，我是一个课外项目的孩子。我妈妈负责起诉虐待儿童的案件，并且非常重视我和我弟弟的安全。如果有一次妈妈不能来接我们，这是一个既定的协议。

首先，她会给程序打电话，让他们知道谁会来接我们(第一个因素)。第二，当朋友或亲戚来接我们时，他们必须告诉我们“秘密词”——一个以前建立的秘密短语，我知道我妈妈只会告诉她信任的人来照顾我们(第二个因素)。

当我听到这个词时，我知道我妈妈真的把朋友或亲戚送来了。

这种 IRL 双因素身份验证(2FA)增加了一层安全性，减少了我和陌生人，甚至没有经过适当授权的非陌生人一起离开的机会。

如果你使用一个提供双重认证的服务，那就接受它。妥协 2FA 还是有可能的，但是难度要大得多。记住，你是想跑得比你的同伴快，而不是跑得比熊快。

## 第五件事:得到帮助是好事

寻求帮助是好事。找个锁匠。弄个家庭报警器。使用可用的工具。用胡椒喷雾跑步。

在软件世界里，这也是事实。有很多组织可以帮助你尽可能的安全。像 [Snyk](http://www.snyk.io) 这样的组织可以帮助你解决开源依赖(我们有一个免费层！)，其他组构建代码扫描器或执行 pentesting，等等。利用你可以利用的资源。你不必一个人去做。事实上，利用你可用的工具是更好的，因为你从他们已经经历的学习过程和发展中受益。

* * *

我写这篇文章是为了帮助那些刚接触安全性，甚至是刚接触软件开发任何方面的人。可能感觉你是从零开始，其实不是。技术不是某个神话般的主题，只能由被选中的接触，或者其掌握过程不同于任何其他技能。你目前的知识和经验是一个很好的起点，你绝对可以学习和提高。前进！

* * *

[【alarm . com】](https://www.behance.net/gallery/8014095/Alarmcom)BY劳伦丹福 授权于[CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/?ref=ccsearch&atype=html)