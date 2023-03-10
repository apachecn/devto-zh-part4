# 构建更好的帮助体验

> 原文：<https://dev.to/cwattrus/building-a-better-help-experience-4lg2>

一年来对 Braintree 的成功改进背后的故事有助于用户体验、学习、指标和我们对 2019 年丰收的计划。

每天我们都需要帮助:帮助寻找未送达的包裹，找出令人困惑的应用程序，排除有缺陷产品的故障，等等。作为制造者，我们不断地提出问题。有时我们自己找到答案，有时我们不得不向人类寻求指导。如果我的经验是可靠的话，那么获得帮助的范围可以是好到让我成为终身的忠实客户，也可以是坏到我不想再有问题。

在 Braintree，我们热衷于为客户提供一些行业最佳的文档和支持。通过写下我们正在做的工作，我们希望改进我们的流程，并邀请用户提供反馈。

在过去的一年里，我们的产品和运营团队一直在密切合作，以改善我们的帮助联系表的每一个部分。我们提供了问题搜索、新的外观、来自文档或商家面板的用户预填充字段、更强大的建议文档、新的易于记忆的域，以及通过与我们其他站点(开发人员文档和支持文章)的共享导航更好的路线查找。“获取帮助”现在是我们几乎所有网站导航的中心，让我们的用户可以快速访问我们的帮助联系表。该表单的主要功能是提供精选内容来帮助用户自助，并在用户需要额外帮助时将他们连接到适当的支持团队。

[![help.braintreepayments.com today](img/63f502bc72f64a03a784f8e87b5b72f7.png "help.braintreepayments.com today")](https://res.cloudinary.com/practicaldev/image/fetch/s--I7-H7H4F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://firebasestorage.googleapis.com/v0/b/pixma-web.appspot.com/o/Images%252Ffiles.link%252Fdev.to%252Fbraintree%2520payments%2520help%2520.png%3Falt%3Dmedia%26token%3D18ef706f-8921-4a87-ad64-840cd103439d)

在深入探讨我们在 2018 年和 2019 年将要做出的改进之前，我们想分享一些衡量成功的方法。

# 我们如何定义和衡量成功

为了量化帮助网站的成功，我们试图准确理解成功的帮助体验对用户意味着什么。在 help.braintreepayments.com 上，用户可以通过访问自助资源或联系人类来实现获得帮助的目标。

**访问自助资源:**当用户选择特定问题时，我们会提供相关文档的链接，帮助用户快速独立地找到答案。这也使得我们的支持团队能够在用户最需要我们的问题上花费更多的时间。无论哪种方式，我们都致力于尽可能高效地提供用户所需的支持，这有助于获得更好的整体体验。

[![The help form suggested docs links](img/6bf63e2be15b83ad7fb07198b5b6dab4.png "The help form suggested docs links")](https://res.cloudinary.com/practicaldev/image/fetch/s--ju08USu4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://firebasestorage.googleapis.com/v0/b/pixma-web.appspot.com/o/Images%252Ffiles.link%252Fdev.to%252Fbraintree%2520payments%2520help%25202.png%3Falt%3Dmedia%26token%3D9e224b0c-08b9-4c2a-bb4c-583cbc7c79d4)

**联系人员:**当用户提交票证或使用电话号码联系我们时，他们可以轻松联系我们的白手套支持团队。

通过结合实现我们两个目标(自助或联系我们)的用户数量，我们有一个成功的衡量标准，在大多数情况下，它代表了对我们的用户重要的事情。

## 2018 年的成功

我们在 2018 年取得的最大成功可以归功于用户采取了前所未有的明确行动。一年来，我们慢慢地发布和迭代我们的功能。这种方法意味着我们可以衡量发布的影响，并确保我们不会增加退出我们正在改进的工作流的用户数量。

[![Experience improvement metrics over the last year](img/a4c85f8098a1acad365c53f470307fac.png "Experience improvement metrics over the last year")](https://res.cloudinary.com/practicaldev/image/fetch/s--orHpeq03--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://firebasestorage.googleapis.com/v0/b/pixma-web.appspot.com/o/Images%252Ffiles.link%252Fdev.to%252Fbraintree%2520payments%2520help%2520metrics.png%3Falt%3Dmedia%26token%3D9f5480e4-ae34-42cf-8fec-8484bb1c3d2d)

下面的趋势线显示了这些改进对用户与建议文档交互和联系我们的综合“成功”指标的影响。

[![Graph showing improvements to help success interactions over the last 12 months. Individual metrics contributed heavily to this success](img/d11ad1eb6edf60c1314a9da09983847c.png "Graph showing improvements to help success interactions over the last 12 months. Individual metrics contributed heavily to this success")](https://res.cloudinary.com/practicaldev/image/fetch/s--vbIYZ-Ka--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://firebasestorage.googleapis.com/v0/b/pixma-web.appspot.com/o/Images%252Ffiles.link%252Fdev.to%252Fbraintree%2520payments%2520help%2520metrics%25202.png%3Falt%3Dmedia%26token%3D034d2b1c-bf0c-4078-a186-020d6ae737af)

# 迄今为止的学习成果

下面总结一下 2018 年我们学到的东西。

**学习 1——一些用户只是想和人类说话:**我们已经看到公司倾向于让联系真人寻求帮助变得更加困难。这种策略从来都不是为了用户的利益。相反，我们选择专注于使联系人类变得容易，并给用户机会提供足够的上下文以从我们的支持团队获得快速帮助。

**学习 2——我们无需门票就能解决简单的问题:**通过改善产品体验，让用户直接从我们的帮助表单中自助，我们可以轻松地为我们的网站带来更多流量，而无需雇用额外的支持人员。

**学习 3——在减少用户工作量和运营成本方面有一个平衡点:**询问用户许多问题来减少内部流程和时间是很诱人的。我们认为用户应该总是做较少工作的人。我们必须更深入地挖掘用户体验(UX)以及内容的相关性和呈现方式，既能让用户轻松找到解决方案，又不会增加支持等待时间。

# 2019 年助力

我们正在吸取这些经验，进一步加强我们的帮助网站。我们 2019 年改进的主题是**超越形式**。我们将打破当前的表单布局，努力改进我们帮助网站的两个关键功能:问题选择和自助内容。

**问题选择:**为了获得帮助，用户必须在我们的表单上选择一个问题，为我们提供提供相关内容所需的上下文，并将请求发送给正确的支持团队。这意味着表单上的问题必须足够简单明了，以便用户能够识别哪个问题与他们的问题精确匹配。

**自助内容:**自助内容的质量及其与用户所面临问题的相关性决定了他们是使用该内容还是提交票证来获得所需的答案。

考虑到这两个特征，我们确定了三个数据点来衡量这些改进的影响。更多用户选择问题，选择更具体的问题，以及更多与自助内容的互动。这三个指标将提高我们在本文开头概述的成功指标。

[![Baseline metrics and expected improvements](img/98f23d0e2cad7ba89903cbdbb72e4f2d.png "Baseline metrics and expected improvements")](https://res.cloudinary.com/practicaldev/image/fetch/s--QQ80PLhL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://firebasestorage.googleapis.com/v0/b/pixma-web.appspot.com/o/Images%252Ffiles.link%252Fdev.to%252Fbraintree%2520payments%2520help%2520metrics%25203.png%3Falt%3Dmedia%26token%3Dff0a5f3c-88c9-414c-a115-350186f080d7)

# 创意&样机

创造一种打破当前模式的体验对我们的设计团队来说是一个有趣的挑战。我们目前正在研究一些早期的想法和模型，随着我们的工作，它们可能会发生巨大的变化。以下是我们正在探索的一些想法:

### 分离问题选择和填表。

要立即获得自助服务，您必须首先开始填写帮助表格。这意味着用户已经做好了填表的心理准备，我们会向他们提供自助文档。许多用户会选择不填写看起来相当健壮的表单，而是通过其他途径寻求帮助(比如我们的支持电话号码)，这意味着他们可能会错过通过我们的自助链接快速找到答案的机会。

我们认为，对选择一个问题的好处更加开放将有助于更多的用户知道他们是否应该努力这样做。

[![An example of how we could introduce users to the help process](img/a50ca9cdaa6975bdabf84db53e9fecee.png "An example of how we could introduce users to the help process")](https://res.cloudinary.com/practicaldev/image/fetch/s--G1kbTP_A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://firebasestorage.googleapis.com/v0/b/pixma-web.appspot.com/o/Images%252Ffiles.link%252Fdev.to%252Fbraintree%2520payments%2520help%2520steps.png%3Falt%3Dmedia%26token%3Da45a4d38-6d2e-4d16-a79b-02985551eba6)

### 更聪明的选择器

[![An example of how we could surface common issues](img/35e9c248da4dd762416c9eea360d07db.png "An example of how we could surface common issues")](https://res.cloudinary.com/practicaldev/image/fetch/s--NzQQjBwO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://firebasestorage.googleapis.com/v0/b/pixma-web.appspot.com/o/Images%252Ffiles.link%252Fdev.to%252Fbraintree%2520payments%2520help%2520selector.png%3Falt%3Dmedia%26token%3D97e2fb04-389b-44e1-b7d0-3a6ddade6ef8)

我们认为，通过使用我们在每年这个时候选择的最常见问题的数据，我们可以改善问题选择体验。

例如，在纳税季节，我们的税务相关问题更受欢迎，应该上升到问题列表的顶部。我们相信，如果用户看到他们的问题在顶部，他们会更倾向于开始与网站互动。

### 更稳健的资源

目前，我们提供指向建议文档的普通链接。我们计划通过包含链接的消息来改进这一点，让用户知道为什么我们认为建议的文档将解决他们的问题。在给我们的用户更多关于这些链接的信息时，更多的用户可能会与我们的建议互动。

[![An example of how we could show suggested documentation](img/8d451b43dec5b8a9029d59f6e05e471b.png "An example of how we could show suggested documentation")](https://res.cloudinary.com/practicaldev/image/fetch/s--GLFVEeT1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://firebasestorage.googleapis.com/v0/b/pixma-web.appspot.com/o/Images%252Ffiles.link%252Fdev.to%252Fbraintree%2520payments%2520help%2520suggestions.png%3Falt%3Dmedia%26token%3Dc4eb1e01-f4f5-4098-9674-360a3ef3aaf5)

## 接下来是什么

随着我们通过在现实世界中测试这些想法获得更多见解，我们将继续在 Braintree 产品和技术出版物上记录我们的旅程。感谢您的关注！