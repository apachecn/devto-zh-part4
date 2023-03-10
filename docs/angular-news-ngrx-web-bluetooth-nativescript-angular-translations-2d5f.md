# 角度新闻:Ngrx、网络蓝牙、NativeScript 和角度翻译

> 原文：<https://dev.to/thisdotmedia/angular-news-ngrx-web-bluetooth-nativescript-angular-translations-2d5f>

Angular 的创新更新和合作伙伴关系每个月都在继续向前发展。社区在不断发展，并为 Angular 和 JavaScript 开发人员引入有趣的主题。在本文中，我们回顾了围绕 Nativescript 和 Angular 集成的起源的对话，使用 web bluetooth 和 IoT 等 API 的新发明，Angular 社区为解决 Angular 中的翻译问题而创建的解决方案，以及流行的 ngrx 的用例。

[![](img/0e2579ccb08a9e7d7dfe80ca8fc59082.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--glhCac-d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2AOggdfZcldPCgDvTea1nS7Q.png)

与会的有 Angular 方面的专家，包括 ngrx 核心团队的 Mike Ryan (@ [mikeryandev](https://twitter.com/MikeRyanDev) )、Angular 核心团队的 Olivier Combe ( [@Ocombe](https://twitter.com/Ocombe) )、Google 开发专家兼发言人 Uri Shaked ( [@Urishaked](https://twitter.com/UriShaked) )以及 native script Progress 的主要开发人员 TJ Vantoll ( [@tjvantoll](https://twitter.com/tjvantoll) )。

# **角形+ ngrx**

*与核心贡献者 Mike Ryan 一起使用 ngrx 和 Angular 的好处*

作为 ngrx 的核心贡献者，Mike 向我们介绍了 ngrx 项目。Ngrx 是一个简洁的 Angular 反应库。由于需要更高的性能，Mike 决定创建一个具有应用程序不可变状态管理的库。一旦完成了这一点，使用可观测量来表达状态变化的想法随之而来。

Mike 解释了使用 ngrx 和 angular 的价值，以及能够管理状态和副作用的好处。他还讨论了 ngrx 和 redux-observable 之间的一些比较。

使用 ngrx 时，考虑应用程序的状态以及这种状态可以有多少种变化方式是很重要的。如果没有太多的状态，并且仅有的更新是用 REST APIs 进行的，ngrx 可能不是最好的选择。然而，当涉及大量用户交互，并且您正在进行大量 REST 调用或者必须处理 webstock 和 images 时，使用 ngrx 是非常有益的。

采用 ngrx 的方法很简单。Mike 解释了开发人员如何着手(缓慢地)引入他们最近构建的应用程序。

[https://www.youtube.com/embed/BxHkI0NUGNQ](https://www.youtube.com/embed/BxHkI0NUGNQ)

# **Angular、NgBeacon、蓝牙**

*结合 Angular，* [Web 蓝牙](https://medium.com/@urish/start-building-with-web-bluetooth-and-progressive-web-apps-6534835959a6)* ，物联网硬件与 Uri Shaked*

尤里·沙克德展示了当你对两个世界充满热情时会发生什么。你创造了有趣又刺激的发明。Uri 引入了 [NgBeacon](https://ngbeacon.io) ，这是一款能够运行 JavaScript 代码的蓝牙低能耗设备。在这次采访中，他展示了该设备如何借助 iPad 上的 Chrome 小工具通过[实体网络](https://medium.com/@urish/exploring-the-physical-web-without-buying-beacons-efae51e36c2e)广播网址。

电子是尤里作为爱好探索的东西。他提到他自己也是初学者，因为他有中国制造的 NgBeacon 的 PCB 板，并学会了如何自己将元件焊接到上面。Uri 分享了他的另一个创作，Bonnie，一个 Shai Reznik 在所有教学视频中使用的吉祥物。几分钟之内，他就可以将 3D 打印的邦妮连接到手机上，并命令它说“嗨”。Uri 随后解释了这是如何实现的。

[https://www.youtube.com/embed/SaAy7kHX2Ig](https://www.youtube.com/embed/SaAy7kHX2Ig)

# **角度平移+ i18n**

*使用角度翻译和 i18n 与 Olivier Combe 一起构建多语言应用*

Angular 的核心团队成员 Olivier Combe 向我们介绍了在 Angular 中翻译应用程序时遇到的困难。当 Angular 2 在 alpha 中发布时，Olivier 决定启动一个项目来帮助他更多地了解这个框架。他的第一选择是建立一个图书馆，所以他决定尝试 angular translate。在发现这是很多人需要的东西后，他继续致力于此。

当公司考虑在应用程序中添加翻译功能时，Olivier 认为有两件事非常重要。首先是翻译是否适合网站设计。第二是目前市场上有哪些解决方案。

他还解释了构建多语言应用程序最困难的部分之一是翻译代码中的字符串。因此，很难有一个数据来源。他还补充说，由于字符或结构的复杂性，一些语言可能更难翻译。Olivier 举的一个例子是，创建一个应用程序，从右到左进行语言解释，而不是从左到右。在这种情况下，人们必须改变墙壁的设计，以适应这种特定的语言规则。

奥利维尔解释说，克服这些问题的最简单方法是创建一个不可知论网站。i18n 也可以是简化角度平移的一种选择。Olivier 目前正致力于改进对这一过程的支持，以便可以在代码中使用。他还希望改进日期和数字的处理方式，完善工具。增加对 i18n 的支持，使其可以在代码中使用，这也是正在进行的工作，最初源于 Google 如何在其 i18n 属性中使用 closure。

[https://www.youtube.com/embed/SK6snNc8eUA](https://www.youtube.com/embed/SK6snNc8eUA)

# **Nativescript、Cordova 和 Angular**

*native script 和 Angular 与 TJ Vantoll 的内部运作背后的历史*

TJ 分享了 NativeScript 最初是如何实现的以及为什么实现的历史。在 NativeScript 之前，Cordova 是众所周知的移动开发框架，非常适合构建简单的 web 应用程序。然而，性能和设备能力很快成为用户和开发者的疑问。在意识到 Cordova 的缺点后，NativeScript 团队决定下一步应该从头开始创建新的东西。那时，Nativescript 作为一种支持构建原生用户界面的技术被引入。

TJ 谈到了在移动设备中使用 NativeScript 与使用 Angular 的其他替代方案之间的差异。他以 Cordova 和 Ionic 为例，详细阐述了这两个框架的功能。TJ 讲述了 JavaScript 如何在带有 JavaScript 虚拟机的移动环境中运行，以及如何打包本机二进制文件，然后在 iOS 和 Android 应用上发布。

TJ 描述的过程非常类似于 Chrome 使用 V8，需要额外的机制来提供关于窗口和文档对象的信息。TJ 还谈到了 NativeScript 和 Angular 之间的紧密集成，这种合作的好处，以及其他框架是否也可以加入这种合作关系。

[https://www.youtube.com/embed/CIrh2IF3nrA](https://www.youtube.com/embed/CIrh2IF3nrA)

请继续关注更多的 Angular，因为新的版本将在这个社区中发布。

有关 Angular 的更多信息，请访问[。](http://angular.io)

*本文由* [崔健](https://twitter.com/heyyyitstk)T3】和* [李美玲](https://twitter.com/ladyleet)共同撰写*

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](https://thisdot.co) 查看我们的服务列表。