# 怀疑谷歌的 reCaptcha？这里有一个受欢迎的选择！

> 原文：<https://dev.to/ambituous/suspicious-of-google-s-recaptcha-here-s-a-popular-alternative-439o>

[hCaptcha](https://hcaptcha.com/) 帮助机器学习公司将他们的数据贴上标签，支付出版商的麻烦，用户不知道其中的区别。

2018 年秋季，谷歌发布了该公司广泛使用的 bot 探测器 reCaptcha 的新版本。reCaptcha v3，就像它的名字一样，在检测机器人方面很棒，但它也有黑暗的一面，因为研究人员怀疑谷歌正在牺牲用户的隐私来支持系统。

幸运的是，对于不信任谷歌的网站所有者来说，除了 reCaptcha 还有其他选择——并且可以使用一点额外的现金。

名为 hCaptcha 的机器人检测器的作用就像用户已经习惯的验证码一样，他们被要求标记他们在不同图像中看到的内容。但是，hCaptcha 并没有展示谷歌的图像(该公司用于训练其机器学习算法的图像)，而是向用户展示了来自数据集的图像，这些图像属于其他公司，这些公司也需要为机器学习应用标记图像。理论上，这项服务对所有人都有帮助:你证明自己不是机器人，同时帮助公司完善算法，网站从整个交易中赚钱。

[![](img/c9805256fecbdbdc0995e032b337ee6e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7YfplboC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.fastcompany.net/image/upload/w_596%2Cc_limit%2Cq_auto:best%2Cf_auto/wp-cms/uploads/2019/07/i-1-90377406-wary-of-googleand8217s-recaptcha-hcaptcha-is-a-popular-new-alternative.jpg)

因为准确的标签对这些公司非常有价值，所以托管 hCaptcha 的[网站根据有多少用户点击 hCaptcha 并成功回答问题而获得报酬。根据他们的流量和攻击他们的机器人数量，网站每月可以赚几千美元。对于需要人们标记数据的机器学习公司来说，以及对于需要验证码安全性和一些额外现金的网站来说，这是一笔好交易。对于用户来说:体验仍然和以前一样，尽管你可以通过仔细观察来区分，因为将会有一个 hCaptcha 标志来代替你习惯的 reCaptcha 标志。如今，每月有 1000 万人在数千个网站上与 hCaptcha 互动，同时为几十到数百个机器学习标签项目提供动力。](https://medium.com/@hCaptcha/how-hcaptcha-calculates-rewards-1195e6f18284)

hCaptcha(“h”代表人类)是 Eli-Shaoul Khedouri 的创意，他是一位资深企业家和人工智能专家，在 2017 年创立了机器学习公司 [Intuition Machines](https://www.imachines.com/) 。在 Intuition Machines，Khedouri 和他的团队为财富 50 强公司构建大规模机器学习算法。虽然 Khedouri 因为保密协议而拒绝分享细节，但他说直觉创造了可以分析视频内容等事情的算法。为了完成这样的任务，直觉的模型需要数百万甚至数十亿个数据点，其中大部分必须由人来标记。一旦他们有了带注释的视频或图像，直觉的团队就可以开始教算法如何识别视频中发生的事情。Khedouri 说:“我们实际上意外地进入了验证码行业，因为我们已经成为了(人工注释)劳动力的大消费者。”。"现有的服务并不是我们真正想要的。"

[![](img/8c651512b426ad640c5c3c5e5dc2e41d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xI9txGX2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.fastcompany.net/image/upload/w_596%2Cc_limit%2Cq_auto:best%2Cf_auto/wp-cms/uploads/2019/07/i-2-90377406-wary-of-googleand8217s-recaptcha-hcaptcha-is-a-popular-new-alternative.jpg)

找到足够多的人来标记如此庞大的数据集是一个严峻的挑战。首先，Khedouri 试图在越南建立他自己的团队来注释数据集。但有时他有足够 12 个人的工作，有时他有足够 50 个人的工作。由于需要标记的数据量根据团队正在进行的项目变化很大，因此拥有一个全职团队并不是最具成本效益的解决方案(尽管对员工来说可能更好)。

取而代之的是，Khedouri 求助于验证码农民——点击工人，他们在互联网上解答验证码，工资只有几分之一。他的团队为验证码农民建立了一个平台，为直觉机器标记数据集，并设计了评估每个农民标记准确性的措施。对于 Khedouri 来说，这是实时标记数据的最有效、最便宜的方法。

hCaptcha 与 Khedouri 仅为直觉机器使用而构建的这个平台有着相同的根源，但截至 2019 年 1 月，它已经向任何需要数据集标签的公司开放。不是验证码农民在做标记，而是像你我这样的互联网普通用户。根据 hCaptcha 的网站，公司将为每张需要一个标签的一百万张图片支付大约 1111 美元。

当然，用户可能会拒绝为公司免费工作的想法，这是科技界的一个普遍现象。Khedouri 建立了 hCaptcha，这样所有向网站支付的款项都将通过 Human Protocol 公布，这是一个运行在以太坊区块链顶部的分散式分类账。这意味着你至少可以看到哪些网站在从你的劳动中赚钱——尽管不知道你标记的数据会被用来做什么。(Khedouri 说，所有的数据标签项目都有保密协议，因为它们大多是为大公司服务的。)

因此，hCaptcha 提供了一个更透明的——如果不是完全完美的——替代谷歌最普及的免费服务之一。通过这样做，它加入了浏览器、搜索和分析领域较小服务的行列，注重隐私的人如果想摆脱这家科技巨头无处不在的影响，可以求助于这些服务。

[![](img/96daa1e001279d398ac6cedd58bf8687.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A5GP-g9F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oei2oxffszmj2p0jvcoi.png)

你可以在这里用他们的 Wordpress 插件很容易地设置它
[https://medium . com/hcaptcha-blog/hcaptcha-plugin-for-Wordpress-now-available-49765536 c69f](https://medium.com/hcaptcha-blog/hcaptcha-plugin-for-wordpress-now-available-49765536c69f)

这篇文章摘自 [FastCompany](https://www.fastcompany.com/90377406/suspicious-of-googles-recaptcha-heres-a-popular-alternative) ，旨在引发对 reCaptcha 和 hCaptcha 及其优缺点等的讨论！