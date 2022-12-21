# Angular 新闻:Angular 4，林挺 Angular，延迟加载，原生移动应用，以及从 AngularJS 到 Angular 的过渡

> 原文：<https://dev.to/thisdotmedia/angular-news-angular-4-linting-angular-lazy-loading-native-mobile-apps-and-the-transition-from-angularjs-to-angular-2o48>

随着 Angular (4)在 javascript 世界中的使用不断增加，围绕许多主题的对话也在发展，包括惰性加载最佳实践、使用 Nativescript 和 Angular 构建本地移动应用程序，以及从 AngularJS 到 Angular 的过渡。

通过这些视频和对 Angular 核心团队成员 Stephen Fluin([@ Stephen Fluin](https://twitter.com/stephenfluin))、Angular 开发专家 Minko Gechev ( [@mgechev](https://twitter.com/mgechev) )、Angular 作者 Manfred Steyer([@ Manfred Steyer](https://twitter.com/ManfredSteyer))、NativeScript 开发倡导者 Jen Looper ( [@jenlooper](https://twitter.com/jenlooper) )和 Angular 发言人 Sergio Cruz ( [@hashtagserg](https://twitter.com/hashtagserg) )的采访，了解 Angular 的最新消息。

[![](img/afc91f0693b2854fcebe472d8759d38a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9x5iFn16--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2400/1%2ADHEXPZgwMplC0JfK09EY9Q.png)

### **棱角分明的状态 4**

Angular 新特性的更新，关于如何利用最新特性的信息，以及一些变化如何使开发人员受益。

### **林挺与明科·格切夫在 Modern.web**

遵循最佳实践对于任何项目的最终成功都是至关重要的。不幸的是，实施它们的过程是手工的并且容易出错——通过代码审查。在演讲中，我将展示如何通过使用 codelyzer 的静态代码分析来自动鼓励最佳实践和验证通用风格。

### **Manfred Steyer 谈角度延迟加载—提高启动性能、挑战、测试模块**

在这次采访中，Manfred Steyer 分享了你需要知道的关于惰性装载的一切。Manfred 围绕 Angular 组织研讨会和培训。

当被问及何时使用延迟加载时，Manfred 说“总是”。原因是，它易于使用，并增加了启动性能的速度。他指出，最好对较大的应用程序使用惰性加载，而不是只有一个或多个模型和组件的小应用程序。

Manfred 谈到了在执行惰性加载时出现的挑战；其中两个是应用程序的后期执行，并以特定场景下的单个服务的两个版本结束。他还简要介绍了如何测试延迟加载模块，以及编写可以延迟加载的应用程序的一般规则。

### **Jen Looper on NativeScript，原生移动应用&有用资源**

Jen Looper 描述了她作为 NativeScript 开发者倡导者的角色，并讲述了如何创建原生移动应用程序不仅涵盖了移动开发，还帮助增强了 Angular 方面的知识。

从银行公司到小型企业，许多公司都在使用 NativeScript 开展业务，因为它是免费和开源的。然而，即使有良好的访问，使用该框架也会带来一些挑战。Jen 提到了两个主要的困难，一个是对如何使用实际框架的困惑，另一个是随之而来的障碍(即没有 webview 或 DOM 可操作)，还有一个问题是“为什么不构建原生的？”。

她建议更好地理解这个框架，包括试用 NativeScript 入门指南、加入 NativeScript slack 频道、参加论坛等等。Jen 还分享了她对 NativeScript 发展的看法。

### **塞尔吉奥·克鲁兹谈从 AngularJS 升级到 Angular 的挑战**

Sergio 讲述了开发人员在从 angularjs 升级到 angular 时面临的困难。他指出，那些遵循推荐路径编写 angularjs 的人(这样向 angular 迁移就不会有麻烦了)，将会经历一个更加简单的迁移过程。

然而，因为 angularjs 不是很固执，所以没有一步一步的教程来帮助开发人员准备升级。事实上，angular 可以有如此多的解释，这是框架转换发生的最大挑战之一。

Angular 与其他框架的另一个不同之处是，两个版本可以在同一个应用程序中运行。让他们互动也是一件棘手的事情。尽管如此，塞尔吉奥解释说，有一些策略可以帮助降低转型的难度。其中包括使用 typescript 或 Webpack 等模块，而不是手动下载脚本，或者在构建过程中引入基于组件的架构，而不是晦涩的指令。

Sergio 还描述了从 angularjs 到 angular 的转变与 Ember 中从全局名称空间到模块的转变之间的相似性。他继续描述了一个开发团队，他们选择将时间完全集中在升级上，并提到了这个过程的持续时间。

当塞尔吉奥被问及他使用什么来帮助 angularjs 进行角度升级时，他推荐了角度升级指南(可在 [angular.io](https://angular.io/docs/ts/latest/guide/upgrade.html) 上获得)。他向开发人员保证，整体迁移听起来可能是一项艰巨的任务，但与其说是一项艰巨的任务，不如说是一项乏味的任务。

有关 Angular 的更多信息，请访问[。](http://angular.io)

*本文由* [崔健](https://twitter.com/heyyyitstk)T3】和* [李美玲](https://twitter.com/ladyleet)共同撰写*

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](https://thisdot.co) 查看我们的服务列表。