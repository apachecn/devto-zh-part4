# 用三个伟大的 web 平台 API 将传统 web 应用向前推进

> 原文：<https://dev.to/justinribeiro/bringing-legacy-web-apps-forward-with-three-great-web-platform-apis-5hd3>

会议室里响起了一声叹息，这是不可否认的，但似乎与公司的环境格格不入。一名工程师的一句话打破了随后刺骨的沉默。

“我们能不能去一个会议，而没有人提到在另一个框架中重写？我们没有时间或预算来做这件事。”

在我被邀请参加的许多工程会议中，这样的说法并不少见。虽然当今流行的 web 框架可能会主导在线对话，但在公司工作的熟练开发人员通常会对他们认为短视的流行观点感到沮丧。

“我们在团队和产品中使用了很多不同的框架，但是当涉及到在我们的产品中构建功能时，流行并不能帮助我们，”一位来自财富 500 强公司的工程师表示，他要求匿名。"我们不想在 20 个框架中开发同样的功能 20 次."

对于许多开发人员来说，web 应用程序有时被称为传统应用程序，为他们的组织和外部用户提供动力，对组织的持续成功至关重要。功能开发从来没有真正停止过，但是开发人员经常努力添加用户想要的功能，迷失在信息的海洋中，没有考虑到不断变化的集成或需求的概念。

当你生活在一系列新开发和 18 个月的重写周期中时，很容易忘记遗留的 web 应用程序。web 上有大量最新功能和范例的例子，突出了最新框架或尖端 web 平台 API 的优点及其易用性。集成到现有的 web 应用程序中？对于许多夹在过去和未来之间的开发人员来说，对于他们现有的 web 应用程序来说，选择似乎既令人困惑又渺茫。

现在的 web 只是框架吗？我现在真的可以使用新的网络平台 api 吗？对于希望继续开发现有 web 应用，但却找不到前进道路的开发人员或团队，您有什么选择？

你并不孤单，我在这里告诉你，web 平台并没有抛弃你、你的用户或你现有的 web 应用程序。但是从哪里开始呢？我们去看表演吧。

## 今夜节目单:三幕渐进式网络平台应用编程接口

开发人员越来越多地探索 web 平台提供的 API 空间。看看 MDN Web Docs 最近的流量统计就知道了，MDN Web Docs 是当今开发人员事实上的 Web 平台 api 信息交换中心，去年用户年增长率为 [21%,月用户数为 1330 万](https://github.com/mdn/pab/blob/master/meeting-notes/2019-01-notes.md)。

这些年来，网络平台及其功能突飞猛进，你的现代网络浏览器包含大约 10，000 个 API。理解和利用这些 API 的任务看起来令人生畏，就好像您正在采用一个新的框架一样。

更复杂的问题是，当涉及到网络平台时，渐进式这个词意味着什么。它经常被扩展为“进步的网络应用程序”，这取决于你与谁交谈，可以在各种技术和意识形态阵营中有广泛的定义。

就我们的意图和目的而言，渐进仅仅意味着我们正在逐步或分阶段地发展。我们有一个现有的应用程序，我们希望添加用户要求的新功能，我们希望有一个 web 平台 API，可能还有一个含糖的库，我们可以使用它，而不必完全重写我们代码库的大部分。

在我们的节目开始时，让我们看看三个特定的 web 平台特性，您可以在您的 web 应用程序中使用它们，无论是传统的还是非传统的。

## 第一幕:用 Web 组件设置场景

当灯光在我们的舞台上亮起时，一组开发人员围坐在一张桌子旁，喝着咖啡。一名信使冲进房间，带来了老板的新要求。我们需要一些数据的视图，设计已经有了模拟，它不仅可以在传统的 web 应用程序中工作，还将被世界另一端的团队在一个新的现代框架中使用。

“我们怎么可能做到这一点？”

事实证明，网络平台已经覆盖了你。输入 [Web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components)，这是一组 Web 标准的名称，包括自定义元素 API、Shadow DOM API 和 HTML 模板 API。虽然您可以单独使用这些 API 中的任何一个，但结合起来，它们为我们提供了实现以下目标的方法:

1.  一个可重用的组件，既可以在我们的遗留应用程序中工作，也可以在广泛的现代框架中工作。担心互操作性？由[罗布·多德森](https://twitter.com/rob_dodson)创建的项目，对每个框架运行一套测试，以识别互操作性问题，并强调已经在其他框架中实现的潜在修复。
2.  一个封装的组件，允许我们在开发时不受遗留 CSS 或 JavaScript 的约束，而遗留 CSS 或 JavaScript 会干扰我们需要实现的特定需求和外观。
3.  一个可组合的组件，允许我们定义自己的声明式 API 供他人使用。

不，这不仅在平台上可用，我们还可以用普通的 JavaScript 编写我们的 web 组件。

```
customElements.define('hi-readers', class extends HTMLElement { constructor() { super(); const shadowRoot = this.attachShadow({mode: 'open'}); shadowRoot.innerHTML = ` \<style\>#hi { color: green; }\</style\> \<div id="hi"\>You all make the web better!\</div\> `; } }); 
```

需要不支持这些 API 的特定浏览器？聚合填料已经覆盖了你。听说过关于 web 组件的不好的事情，以及它们与 framework X 相比是如何的无用？如今，Chrome 中 10%的页面加载使用了 web 组件，Salesforce 等公司都在使用 web 组件。

寻找更好的 web 组件体验？LitElement 是一个简单的基类，用于创建快速轻量级的组件。来点香料怎么样？[肉豆蔻](https://github.com/abraham/nutmeg)让我们用 TypeScript、Karam test runner 和 web 组件最佳实践来生成一个普通的 web 组件框架。使用 Vue.js？[他们的 CLI](https://cli.vuejs.org/guide/build-targets.html#web-component) 有 web 组件作为输出选项。Ionic Framework 团队构建了默认输出 web 组件的[模板](https://stenciljs.com/)。甚至 Angular 现在也有了 [Angular 元素](https://angular.io/guide/elements)，打包成定制元素。不管您的生态系统如何，使用 web 组件从未如此简单。

当谈到进步的 web 平台特性时，web 组件被列为最有用的东西之一，您现在可以使用它将您的遗留 Web 应用程序带入未来。

## 第二幕:用网络组装对抗你的计算对手

随着帷幕拉开，庆祝活动正在进行。这个令人惊叹的 web 组件工作得非常顺利，在遗留应用程序和最新的框架中都能很好地工作。当信使气喘吁吁地再次冲进房间时，开发人员聚在一起开香槟庆祝。

“关于 web 组件的消息在公司里传开了。还有另外两个团队想要使用它，但他们需要像 Windows 应用程序那样处理数据的组件。头儿说我们必须实现它。"

瓶子现在被轻轻地放在桌子上，房间里爆发出一阵咯咯的声音，所有的叫喊声都能被听到。一台电脑被扔出窗外。

“我们不能让这种情况发生，那个应用程序是 C++！”

" JavaScript 很快，但也没那么快."

“我们不能这样！”

虽然这可能看起来有点牵强，但在许多组织的大锅应用程序中，都是旧的本地应用程序，充满了过多的自定义遗留 C/C++库，用于处理 web 平台长期以来无法处理的计算任务。没有了；期待 [WebAssembly](https://developer.mozilla.org/en-US/docs/WebAssembly) ，一个安全的、可移植的、低级别的编译目标，它补充并运行你的 JavaScript。

鉴于选项的范围，将库编译成 WebAssembly 可能会令人望而生畏，但将其分解成基本的开发步骤会更容易理解前进的道路:

1.  用您希望在 web 应用程序中使用的 API 表面包装您的 C/C++库。
2.  使用类似于 [Emscripten 的工具来编译](https://developer.mozilla.org/en-US/docs/WebAssembly/C_to_wasm)，允许我们限制编译 wasm 模块的复杂性。
3.  包括生成的 JavaScript 和 wasm 二进制文件，并简单地包含到您的 web 应用程序中

我们构建的结果输出可以很容易地在 web 应用程序中我们需要的地方实例化。

```
\<script src="a.out.js"\>\</script\> \<script\> Module.onRuntimeInitialized = (\_) =\> { const computeTPSreport = Module.cwrap('tpsReport', 'number', ['number']); console.log(computeTPSreport(201902)); }; \</script\> 
```

有了目前所有主流浏览器的支持，您可以开始移植您的遗留库，并在您的 web 应用程序中使用它们。Emscripten 现在输出 WASM ，并且有许多助手和工具来帮助你。想在不需要构建工具的情况下试水吗？今天就用 [webassembly.studio](https://webassembly.studio/) 试用 WebAssembly，让你不用离开浏览器就能编写、构建和测试。考虑一下，不使用遗留库，只编写一个性能更好的算法？ [Rust 有一个惊人的指南](https://rustwasm.github.io/book/)用于构建 web 汇编，像 [Go 和许多其他语言正在增加支持](https://github.com/golang/go/wiki/WebAssembly)。

## 第三幕:与服务人员一起解决(更快)

随着我们的戏剧接近尾声，我们的演员已经发布了一个 web 组件，其中包含一些用于计算喷气燃料的 WebAssembly。用户很高兴，开发人员也为发货松了一口气。就在这时，一份报告来了。

“这里说，其他一些应用程序的加载速度比我们的传统 web 应用程序快得多，而且它们可以离线工作。我们能做到吗？”

网络平台再次通过[服务人员](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)来帮助我们。虽然被认为是进步的 web 应用程序故事的一个支柱，但服务工作者只是我们作为开发人员控制网络请求和缓存的一种手段。它们是 web 平台多年来缺失的底层网络代理，使我们能够提高性能，使我们的 web 应用程序更能适应各种网络条件。

通常，这是一个服务人员的简单例子，突出了网络平台给我们带来的直接好处。然而，在许多公司的经验告诉我，即使是最简单的服务人员的例子也常常太低级，让开发人员感到害怕。再加上各种使服务人员对开发人员来说很难的问题(“我如何刷新这个东西，为什么它不会放手”)，我已经采取了一种新的方法，而不是谈论低级的样板文件。相反，我向您提供了许多开发人员更容易访问的备选库——work box。

Workbox 是一个开源库，它为服务人员定义了一系列最佳实践，并去掉了我们不得不编写的复杂的样板文件。[项目](https://github.com/GoogleChrome/workbox)是谷歌工程师[杰夫·波斯尼克](https://twitter.com/jeffposnick)和[菲利普·沃顿](https://twitter.com/philwalton)(以及许多其他提交者)多年工作的结晶，他们磨练并简化了我们作为开发人员可能想要用来使我们的网络应用程序更快更有弹性的常用方法，从缓存我们的 CSS 和 JavaScript，到离线处理那些讨厌的谷歌字体。

入门有多容易？你只是一个进口货和一条路

```
// sw.js importScripts('https://storage.googleapis.com/workbox-cdn/releases/4.3.1/workbox-sw.js'); // serve from cache, update in background workbox.routing.registerRoute( /\.(?:js|css)$/, new workbox.strategies.StaleWhileRevalidate(), ); 
```

抛开人们常说的“服务人员实际上只为单页应用程序设计工作”不谈。有了配方和缓存策略的深入列表和 4.0.0 版本的 T2 工作盒窗口来帮助简化注册，工作盒允许你为任何 web 应用带来弹性和性能，不管架构模式如何。这个图书馆的美？它只是建立在网络平台之上。

## 谢幕:Web App 清单

当演员们鞠躬时，你从座位上高兴地跳起来，大喊“再来一次！”web 平台提供了如此之多的东西，但是这些 API 轻松地嵌入到您现有的 web 应用程序中，让您想要更多。

来自舞台的呼喊:Web 应用清单。

[Web 应用清单](https://developer.mozilla.org/en-US/docs/Web/Manifest)是一个基于 JSON 的文件的规范，它保存了您的 Web 应用元数据。这个文件帮助我们的网站[在移动设备和桌面](https://medium.com/@kennethrohde/progressive-web-apps-coming-to-all-chrome-platforms-80e31272e2a8)的主屏幕上提供安装体验。它是简单的格式和一个单一的文件，给我们的用户一个非常轻松愉快的体验。

```
// manifest.json { "name": "FAA Flight Planning", "short\_name": "FAA Plan", "description": "Build a flight plan.", "start\_url": "/", "theme\_color": "#f1f1f1", "background\_color": "#f1f1f1", "icons": [{ "src":img/manifest/faa-512.png", "sizes": "512x512", "type": "image/png" }], "display": "standalone" } 
```

从那里，用户可以获得稍微好一点的图标和桌面体验。从那里，你将能够扩展你的工具箱服务人员的使用，并利用 [beforeinstallprompt](https://developers.google.com/web/fundamentals/app-install-banners/#listen_for_beforeinstallprompt) 来处理你想要如何处理安装到主屏幕的体验，以获得更加进步的体验。

## 网络在发展，但并不要求你放弃一切

对于一些人来说，我在上面选择强调的 API 可能看起来很奇怪。它们不是来自我的观点，而是来自我自己与许多组织合作的经验，这些组织感觉他们被困在非常有用但往往孤立的 web 应用程序上。

web 平台及其 API 在不断发展，为我们提供了更多的 API 和工具来为我们的最终用户构建令人惊叹的体验。仅仅因为您的 web 应用程序已经存在了十年，并不意味着您必须放弃一个有用的代码库来进行基本的重写。通过采用渐进的方法并使用平台 API 和轻量级库，您可以开始使用 web 平台提供的更多功能。

最终的结果是:更快乐的用户和更快乐的开发者体验。