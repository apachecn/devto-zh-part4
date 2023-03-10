# 我为什么选择 Angular🔧

> 原文：<https://dev.to/angular/why-i-chose-angular-hk>

## TL；速度三角形定位法(dead reckoning)

我选择 Angular 是因为我们是一个伟大的团队。它提供了一个成熟的平台，在我关注用户的同时，处理非功能性需求，如兼容性、可维护性和可测试性。

## 角解决什么问题？🤔

前端开发变得越来越复杂。

自从我们认识到网络的潜力，我们的界面变得更加丰富，我们的功能变得更加复杂，但是随着我们的需求越来越大，我们的挑战也越来越大。

为了给你一个思路，这些是我今天构建大型企业应用程序时必须考虑的一些方面:

*   A11Y ♿️:使每个人都可以访问网站，包括但不限于色盲、视力障碍、运动障碍或听力障碍的人。跨浏览器💻:确保你的网站可以在几乎所有浏览器上无缝运行，或者至少是渐进地运行:Chrome、Safari、Edge、Internet Explorer 等。
*   自适应🖼:提供可以适应各种屏幕尺寸(电视或小型设备)、分辨率(retina、2x 图像)和类型(手机、平板电脑、台式机)等的 UI 和 UX。
*   使用多种语言的👄:建立能适应不同语言结构的网站，如 RTL 和 LTF、复数、货币等。
*   表演🏃‍♀️:创建网站，可以在不同的网络条件下，如 3G 和低内存设备上及时呈现。

这只是对最终用户而言——开发人员还必须处理许多事情:

*   可维护性👩‍💻:处理可以轻松达到数百万行代码的代码库，同时在模块化、搭建和标准方面确保适当的开发人员工效学。
*   建筑物🚜:确保您的应用程序包是轻量级的，这样您就不会超出预算，否则会影响您的性能，并密切关注任何编译错误和构建时间。
*   可测试性🧪:确保单元测试和端到端测试完美运行，同时确保尽管应用程序很复杂，但创建新的测试用例仍然很简单。

> 请注意，对于某些应用程序，这只是您需要的东西的摘录，对于其他一些应用程序，您可以完全忽略这些需求。

对于第一次加入软件行业的人来说，这简直让人不知所措。

作为一名专业人士，我有责任理解这些挑战，并知道如何应对它们。然而，我不想每次创建一个新的应用程序都要花几个小时来解决这个问题。🤷‍♂️

这是我的问题，也是 Angular 最擅长的。

## Angular 是做什么的？🤔

Angular 做了很多事情，但也许这张照片可以给你一个更好的想法:

[![](img/e78a775025feb5cf0d91ede5e01e4269.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8rp0mKb0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h9qhjhinnmmhfx54xsh3.png)

上面那些漂亮的图标代表了 Angular 提供的一套工具。让我们来看看其中的一些:

*   💻CLI:根据大型企业的说法，Angular CLI 是 Angular 配备的最佳功能之一。它允许你用一个简单的命令来搭建组件、服务和模块，但是当然，这本身并没有给人留下深刻的印象。CLI 的真正价值来自更重的命令，例如 ng add @angular/pwa，它可以自动将您的应用程序转换为渐进式 Web 应用程序，或者 ng update @ angular/core @ Angular/CLI，它可以自动将您的应用程序更新到 Angular 的最新稳定版本。多亏了 Schematics 的强大功能，所有这些都成为可能。
*   🚜Schematics:你可以把 Schematics 想象成 Angular 的自耕农，但是拥有超能力。Schematics 是一个代码生成器，它允许您创建和转换代码。例如，您可以创建一个示意图，用访问器和 all 为您生成整个类，而不是一遍又一遍地创建您的 TypeScript 模型。然而，这只是一个简单的例子——您也可以将它用于复杂的逻辑，比如重构一个大型应用程序，以便在所有组件中使用外部模板而不是内联模板！手工做它花了我 2 个小时，用图表做它只需要几分钟😙。
*   🖼 Angular Material:这是一个由 Google 开发的组件库，可以让您访问一组开箱即用的经过全面测试的组件。它最初以 Angular Material 设计系统为主题，但它也提供了一个 CDK(组件开发工具包)，让您有机会使用您需要的任何设计系统。

[![](img/dc34431d881ccdd2bc170bf831829284.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MmoO2DsU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pp0xby88fyzmt0uijdbs.png)

*   📞Http 模块:Angular 团队知道 HTTP 调用存在于大多数 web 应用程序中。这就是为什么 Angular 附带了一个模块，使您更容易使用它。这是基于可观的，所以你可以以任何你喜欢的方式组成你的电话。此外，它还有 HTTP 拦截器，允许您轻松地转换传入/传出的请求。
*   👄i18n: Angular 还通过 Angular CLI 提供内置功能，帮助您创建多语言网站。它读取你的模板并生成 XLIFF(或 XMB)文件，可用于文本、数字、日期和货币的翻译。一旦您设置了翻译，CLI 将为您创建相应的版本，无需任何额外的工作。
*   🧪·卡玛、茉莉和量角器:单元测试和端到端测试是 Angular 团队的首要任务。这就是为什么 Karma 已经在每一个 Angular 项目中进行了配置，使用 CLI 创建。如果您愿意，可以使用 Jasmine，或者切换到您喜欢的任何其他框架。最后，您所要担心的就是编写您的测试。
*   🗺路由器模块:无论您是延迟加载、预加载还是按需加载路由，Angular CLI 都有一个模块可以让您轻松完成。它提供了一个可维护的结构，使得具有大量路由配置的应用程序易于处理。你应该只关心是什么，而 Angular 关心的是如何。
*   💥反应式表单模块:表单是 web 应用程序中最常见的构件之一。有些很简单，如登录表单，有些很复杂，有些字段值依赖于其他字段值，这些值需要在客户端和服务器端进行实时验证…😲。那么，反应式表单模块可以帮助您减少实现这一目标所需的样板代码量，同时允许您将逻辑排除在模板之外。

当然，其中一些功能还远非完美，但就目前而言，它们为我节省了大量时间。

## 那我应该用棱角分明吗？🤔

工具是否合适取决于您当前的需求、环境和挑战。

对我来说，Angular 非常适合大型挑战。尽管如此，网络难道不是一个巨大的挑战吗？ [Ryan Grove](https://twitter.com/yaypie) 在这里提供了一个有趣的观点:

[![](img/be42448898adbd924424b9975e1ab730.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--47jx1bq0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/40s6f4wjpq9unzle3cai.png)

如果今天我们有更好的工具来帮助我们做好这件事，那么为什么不使用它们呢？一个很好的理由是成本。对于一些人来说，掌握 Angular 可能比学习其他框架花费更多的时间。

最终，是否为此买单取决于你，但亨利·福特的名言却派上了用场:

[![](img/eea61cfe4f85a466df0127af7bddabc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eKNm_wLj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ebgjbqwy2fle8oiv3x2z.jpg)

如果你需要 Angular，但你没有学，你会意识到你学会了如何处理 Angular 解决的每一个问题，但你仍然不了解 Angular。

* * *

感谢阅读！🎉

如果你对 Angular 有疑问，或者不确定 Angular 是否适合你，请联系我！我很乐意帮你解决这个问题！🚀