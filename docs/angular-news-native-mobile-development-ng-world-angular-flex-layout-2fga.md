# ANGULAR NEWS:原生移动开发，Ng-World，Angular Flex-layout

> 原文：<https://dev.to/thisdotmedia/angular-news-native-mobile-development-ng-world-angular-flex-layout-2fga>

更多更新来自 Angular 社区。加入 Sebastian Witalec、Minko Gechev、Thomas Burleson、Tara Manicsic 和 Deborah Kurata 的讨论，他们讨论了 JavaScript 中许多令人兴奋的新发展。观看和阅读 NativeScript 及其对移动开发的重要性，了解 Minko 的 Ng-World、Angular 编译器、Angular flex-layout、Kendo UI 组件和 Angular 模块。

**Nativescript +原生移动开发**

*为什么 NativeScript 是 Sebastian Witalec 的强大原生移动开发工具*

Sebastian Witalec 对摄影和 futbol 有着浓厚的兴趣，他一直活跃在 JavaScript 社区，专攻 NativeScript。Sebastian 最初是通过一个朋友发现这个框架的，他称之为混合开发。他很快就被这个框架的能力迷住了，它既能使用 JavaScript 代码，又能使用原生 UI 来构建移动应用程序。据 Sebastian 称，NativeScript 于 2015 年 2 月/3 月左右开源。因为当时没有角度集成，所以用 JavaScript(带 xml)和 CSS 来创建移动应用。

Sebastian 讲述了开发人员如何从头开始创建 NativeScript 应用程序。他说，根据个人情况，他/她可能能够仅凭自己的固有知识创建一个模板。有了这个模板，他们就可以继续按照相同的结构创建在 iOS 和 Android 上运行的插件。

他提到从零开始创建组件的开发人员的伟大之处在于，他们通常是愿意回馈社区的人。Sebastian 对插件给出了更多的见解，指出了与 Angular 良好集成的好处，并就 Ember 是否有机会与 NativeScript 集成发表了意见。

[https://www.youtube.com/embed/zcNEEUuOKSg](https://www.youtube.com/embed/zcNEEUuOKSg)

**Ng-World，Angular 编译器，以及 Angular 的自动迁移工具**

*在 Minko Gechev 的 3D Ng 世界中可视化您的代码，并了解 Angular 的编译器*

进入一个 ng 世界，模块被视为花园，树作为组件存在。Minko 的作品展示了 3D minecraft 体验，使用 Angular 的编译器以可视化源代码的方式非常规地构建。它不仅允许你浏览你自己的源代码，还能判断你工作的质量。

Minko 讨论了 Angular 编译器的许多好处，包括验证组件中声明的样式的能力。这个动作本身就有助于减少你的应用的捆绑包大小，并且带来其他好处。

目前，Minko 正在开发一个自动化迁移工具。随着 Angular 4 的发布，ng-template 应运而生。然而，当搜索和替换旧模板时，会出现源代码中断的问题，从而将它们转换成 ng 模板。这就是 Minko 如何想到创建一个迁移工具的想法，它结合了编译器和静态代码来帮助完成这个过程。Minko 还解释了编译器如何将应用程序转换成更高效的版本。

Minko 发现自己被 Angular 社区所吸引，这是因为他对静态类型语言如 TypeScript 的热情，以及他对 Angular 及其平台的强烈钦佩。

[https://www.youtube.com/embed/JJftfaNu2rU](https://www.youtube.com/embed/JJftfaNu2rU)

**角度柔性布局**

*了解采用角形材料的角形柔性布局领导托马斯·伯利森*

托马斯·伯利森是 angular material 的团队领导，ThoughtRam 的讲师，Angular Flex Layout 的设计师。他认为布局是开发人员在使用 Angular 构建应用程序时面临的挑战之一。

当一个应用程序的功能布局建立后，必须创建一个用户界面体验。这个过程依赖于动画和组件。考虑如何根据浏览器窗口的变化来调整范围和大小也很重要。

以前，角度动画一直是开发人员制作体验动画的直观 API。Angular Flex library 后来成为一个允许开发者在多种设备上布局应用的库。

Flex Layout 是第二代或第三代试图提供 CSS 的替代品。Flexbox CSS 最终成为了一项技术，它可以识别页面应该如何在页面上流动(水平还是垂直)以及组件应该如何相对于彼此调整大小。

尽管这个库很有帮助，但它确实有一些胁迫因素。如果个人不习惯使用 CSS，他们必须熟悉样式表语言。一旦他们完成了这个挑战，他们需要学习 Flexbox CSS 的细节。

Thomas 详细阐述了开发 Flex Layout 的目标、问题和特性。他描述了该库的 API 和要使用的指令，以及为使该库更好而包含的响应特性。他还回答了是否支持 Angular Material 2 以及开发人员是否可以立即开始使用 Flex Layout。尽管图书馆的新功能正在开发中，而且还有许多问题有待解决，但 Thomas 鼓励个人使用该图书馆，因为它易于使用且非常有用。

[https://www.youtube.com/embed/s9K6oamxMNI](https://www.youtube.com/embed/s9K6oamxMNI)
**原生组件为棱角—剑道 UI**

*剑道 UI——用 Tara Manicsic 为 Angular 定制原生组件*

如果你曾经希望能更快地开发健壮的应用程序，你的愿望现在实现了。进步公司 Telerik 今年发布的 Kendo UI 为 Angular 创建了预建的、功能齐全的本地组件。

剑道 UI 组件是从头开始构建的，因此您可以立即利用树摇动或通用渲染等功能。一些支持 Angular 开发人员的更流行的组件包括网格和图表组件。

Grid 用于组织客户带来的数据，并使他们能够管理如何查看、排序和分页信息。它有一个完整的 API，给创作者更多的自由来定制和改变他们的应用程序。随着更多特性的加入，网格变得更加健壮。

图表组件有助于快速可视化数据，并且易于集成到现有代码中。组件可以一个模块一个模块地安装，并添加到开发人员现有的模板中。每次输入新数据时，图表都会刷新。

特别是这两个组件可以改变用户正在进行的许多交互，并且是 Kendo UI 如何使 Angular 开发人员更快地构建更健壮的应用程序的很好的例子。

[https://www.youtube.com/embed/hQVUwpTbkJk](https://www.youtube.com/embed/hQVUwpTbkJk)

**模块、延迟加载和捆绑包**

*ES2015 模块与 Deborah Kurata 的角度模块*

您可能想知道 ES2015 模块与角度模块有何不同。我们采访了 Deborah Kurata，她解释了两者之间的细节和差异。

借助 ES2015 模块，开发人员现在可以创建代码文件，并使用导入和导出语句从该文件导入或导出。ES2015 模块位于微观级别，只是文件。同时，Angular 模块更加宏观，提供了一种将应用程序分解成大块的方法，每个大块包含应用程序执行其功能所需的信息。

利用 Angular 模块的最佳方式是从 app 模块开始。这个特殊的模块将应用程序结合在一起，当开发人员构建他们的应用程序时，它可能会变得太大。当这种情况发生时，开始将应用程序分解成更小、更独立的模块是很重要的。有时，这些被称为特征模块。

创建特性模块是有益的，因为许多人使用 Angular 来构建企业范围的应用程序，并且需要灵活性来将他们的应用程序分成更小的部分，以便他们可以更好地管理和跟踪各个团队正在进行的工作。此外，利用 Angular 模块并将它们作为单独的包延迟加载到您的应用程序中，可以而且应该提高应用程序的整体性能。

[https://www.youtube.com/embed/ocZjWw_LImg](https://www.youtube.com/embed/ocZjWw_LImg)

我们希望你喜欢这篇文章。有关 Angular 的更多信息，请务必查看 [http://angular.io](http://angular.io) 。

*本文由[崔健](http://twitter.com/tkien001)和[李美玲](http://twitter.com/ladyleet)T5】共同撰写*

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。