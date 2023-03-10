# 开发人员必须做的 5 个安全提示

> 原文：<https://dev.to/terceranexus6/5-must-do-security-tips-for-developers-1a7>

因此，我作为一名安全分析师和审计员已经工作了一段时间，我遇到了很多让我感到沮丧的事情。有时我会想“如果开发人员及时注意到这个安全提示……”然后，为什么不讨论一些开发人员可以采用的安全技巧呢？我列出了五条建议，但是你可以随意添加自己的建议！

## 提示 1:注意默认管理页面

我无数次遇到过带有`/myadmin.php`的页面和带有默认值的类似管理页面。使用框架是一个好主意，但是注意这些细节是很重要的。攻击者可以使用暴力绕过管理页面的登录，造成混乱。

[![](img/d255e420c4211961ef1bab150f9ba7b8.png)](https://i.giphy.com/media/xTiTnGeUsWOEwsGoG4/giphy.gif)

## 提示 2:请，请多请强密码

我的意思是，我知道记住强密码是一项非常困难的任务，但是啊！我们已经为那件事做了准备。非常请，如果你仍然有疑问，只是检查巨大的泄漏。这似乎不重要，但事实并非如此。

[![](img/f1d761ad296ee7ee103b650635637d6c.png)](https://i.giphy.com/media/EGe7H8HTtw3Cg/giphy.gif)

## 技巧 3:避免“它是安全的，因为它在一个容器里”

没有。安全系统不是这样的。真的。例如，如果你们正在使用旧版本的资源(我遇到过很多次)，例如 ElasticSearch，你可以使用 [curl 命令来进入应用程序](https://nvd.nist.gov/vuln/detail/CVE-2015-1427)，或者其他[很多东西](https://www.elastic.co/community/security)。我知道寻找漏洞不是你的工作(这就是为什么你应该为此聘请专家)，但你可以同时使用 [CoreOS 和 Clair](https://github.com/coreos/clair) 来处理基本的漏洞评估，例如我在这里描述的。

[![](img/be87464806b9b191123498df53116566.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ds3oQHgi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.tenor.com%252Fimages%252F7d1a17a8cfc4766ea5ef1147db15cd69%252Ftenor.gif%26f%3D1)

## 提示 4:过滤所有的东西

请注意用户可以在你的应用程序中输入什么。如果你不知道，有一种攻击叫做[跨站脚本](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS)) (XSS)，它可以被用作许多其他攻击的一部分，这是由于缺乏过滤。不要让用户使用脚本元素，就这样可以省去那么多问题。

[![](img/bcc497cb09ff1106266aa263d13be216.png)](https://i.giphy.com/media/3o7WTwcO5cLAVL5Z7O/giphy.gif)

## 提示 5:问

好的，所以我想了很多关于最后一个问题的建议，比如不要让时尚超过你，不要因为新技术听起来很花哨就对它们进行适当的审核，或者注意服务器加固。但最后，我想到了最重要的，也是被广泛遗忘的:问。不知道一件事情是完全正常的，当它是一个安全疑问时，有更多的可能性，这是正常的，因为作为一个开发人员，你可能不知道一些细节。没关系，这就是审计和安全分析的原因，但即使应用程序将通过安全专家的检查，也不要害怕在开发过程中询问，因为一些细节可能是结构或概念安全错误，这些错误在早期过程中可以很容易地解决，但当分析师在最后阶段接受时却是一片混乱。在早期接受安全建议是一个健康负责的决定。我有时觉得开发人员把我视为要与之斗争的敌人，有人破坏了“他们的孩子”,但我只是试图让它工作，当你从一开始就让安全人员参与进来时，这就更容易做到。

[![](img/aaec5c4149bae26ffc684d9571b16f24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U2-TPoiF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://giphygifs.s3.amazonaws.com/media/7K3p2z8Hh9QOI/giphy.gif)

我希望你们喜欢这些提示，并随时添加更多。你在工作中用安全提示挽救了局面吗？我很乐意收到你的来信。