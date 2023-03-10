# 2020 年你应该知道的 8 个前端 JavaScript 趋势和工具

> 原文：<https://dev.to/giteden/8-frontend-javascript-trends-and-tools-you-should-know-for-2020-j7n>

## 前端开发人员应该开始学习的八个固执己见的预测…

JavaScript 世界发展迅速。

前端开发(和 web 开发)的世界正在飞速发展。今天，如果你不在 top 或 Webpack、React Hooks、Jest、Vue 和 NG 元素之上，你会开始感觉到差距在扩大。但是，事情正在发生变化。

虽然前端丛林中的开发人员和技术人员的数量逐年激增，但生态系统渴望实现标准化。新技术和工具的出现已经在改变游戏规则。

可以肯定地说，总的趋势将是 UI 标准化、基于组件的模块化和组合，这将影响从样式到测试甚至状态管理的一切，以及总体上更好的模块化。这将包括围绕 web 组件、es 模块、用于样式或状态管理的以组件为中心的工具等构建的技术。

这是对未来几年前端开发状态的一个自以为是的简短和片面的观察。很自然地，它错过了一百万件事情，所以请不要客气地跳到下面，加入你的见解。

### 1。框架不可知论 Web 组件

所以基本上，这就是未来。为什么？因为这些纯 web 组件是框架不可知的，可以在没有框架或任何框架拼写标准化的情况下工作。因为它们摆脱了 JS 疲劳，并且得到了现代浏览器的支持。因为它们的包大小和消耗将是最优的，并且 VDOM 渲染是令人兴奋的。

这些组件提供了 Custom Element，一个 Javascript API，它允许您定义一种新的 html 标记、指定布局的 HTML 模板，当然还有本质上特定于组件的 Shadow DOM。

在这一领域需要了解的突出工具有 [Lit-html](https://github.com/Polymer/lit-html) (和 Lit-element)[stencil js](https://github.com/ionic-team/stencil)、 [SvelteJS](https://github.com/sveltejs/svelte) ，当然还有 [Bit](https://bit.dev) ，用于可重用的模块化组件，可以在任何地方直接共享、使用和开发。

当考虑我们 UI 开发的未来，以及模块化、可重用性、封装和标准化的原则在组件时代应该是什么样子时，web 组件就是答案。下面了解更多。

*   [2019 年开发 Web 组件的 7 款工具](https://blog.bitsrc.io/7-tools-for-developing-web-components-in-2019-1d5b7360654d)
*   [2019 年你应该知道的 9 个 Web 组件 UI 库](https://blog.bitsrc.io/9-web-component-ui-libraries-you-should-know-in-2019-9d4476c3f103)
*   [Web 组件原型:构建一个 RSS 阅读器](https://blog.bitsrc.io/prototyping-with-web-components-build-an-rss-reader-5bb753508d48)

### 2。框架战争的未来？

所以我们并不打算深究“谁更好，为什么”，你可以在下面找到这个问题的更多答案。相反，我们将后退一步，注意更大的画面。围绕组件的前端技术的整体“市场份额”正在增长。不断地。新开发人员的加入率也在快速增长，并且有更多的空间供工具采用。

那么，从现在起的 5 年内，哪个框架将会统治世界呢？没人知道。但是，可以肯定地说，在 web 组件统治 dom 的原生 JS 生态系统中，它将是一个最佳位置(双关语！😈).React 位于 NPM 下载量的首位。然而——看看[这些数字](https://w3techs.com/technologies/comparison/js-react,js-vuejs)。似乎在实际的 web 使用中，差距很小。

令人震惊，对吧？😃

随着与框架无关的 web 组件的未来标准化，人们可能会怀疑它对 UI 框架战争的影响。是的，React 不是一个框架…我们知道。

利用真实数据深入研究框架基准测试:

*   [小型 web 应用的 Angular、React 和 Vue 基准测试](https://blog.bitsrc.io/benchmarking-angular-react-and-vue-for-small-web-applications-e3cbd62d6565)

*   [React vs Angular vs vue . js—2019 年选什么？(更新)](https://medium.com/@TechMagic/reactjs-vs-angular5-vs-vue-js-what-to-choose-in-2018-b91e028fa91d)

### 3。组件隔离、重用和组合

[Bit(开源)](https://github.com/teambit/bit)将你的组件(或任何可重用的 JS 代码)隔离并转化为共享的构建块，你可以在你所有的项目和应用中使用和共享。神奇的是，您还可以使用 Bit 从不同的项目开发相同的组件，同时获得对源代码更改和整个依赖图的完全控制。

更简单地说，有了[位](https://bit.dev)，你可以立即在另一个项目中使用一个项目的组件，从两个项目中开发和修改它，并同步修改。当作为一个团队工作时，这个工作流程通过 [bit.dev](https://bit.dev) 得到增强，bit 的组件中心，在这里你可以组织和共享你团队的代码。

该中心提供了您在组件上共享和协作所需的一切，从漂亮的搜索和发现体验到实时组件游乐场、全面的 CI/CD 支持等等。

[https://www.youtube.com/embed/E5lgoz6-nfs](https://www.youtube.com/embed/E5lgoz6-nfs)

借助 Bit，您可以构建应用程序，全面、即时地访问您的团队和开源社区编写的所有组件，并即时共享新组件或建议对现有组件进行更新。哇哦。

[teambit/bit](https://github.com/teambit/bit)

### 4。ES 模块和 CDN

ES 模块是在浏览器中使用模块的标准，由 ECMAScript 标准化。使用 ES 模块，您可以轻松地将功能封装到可以通过 CDN 等使用的模块中。随着 Firefox 60 的发布，所有[主流浏览器都将支持](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/) ES 模块，Node 团队正在致力于为 [Node.js](https://nodejs.org/en/) 添加 ES 模块支持。此外，[面向 WebAssembly 的 ES 模块集成](https://www.youtube.com/watch?v=qR_b5gajwug)将在未来几年推出。试想一下，用 Bit 隔离的 JS 组件，通过 [bit.dev](https://bit.dev) 经由 CDN 消费。

*   [ES 模块:卡通深潜- Mozilla 黑客-网络开发者博客](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)
*   [导入](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)
*   [德诺兰/德诺兰](https://github.com/denoland/deno)

### 5。组件级的状态管理

那么……国家管理有什么新变化？最终，我们只是通过 Redux 全球商店的一切反正对不对？

但是，这很难充分利用组件的模块化和可重用性。从这个角度来看，像 MobX 这样的项目提供了一种有趣的、更具反应性的方法。React 的新上下文 API 和钩子意味着您不需要第三方库，并且可以在功能组件级别管理状态，从而提高模块化和可重用性。

因此，展望未来，试着从封装组件的角度考虑状态管理，而不是从全局应用程序存储的角度。

*   【2019 年反应状态管理状态
*   Redux 或 MobX:试图化解困惑

### 6。将组件样式化为组合

因此，在过去的两年中，有很多关于样式组件的讨论。从内联 CSS 或 CSS 模块到 JS 和样式化组件中的 CSS，甚至是像 stylable 这样的中途解决方案，选项非常多。

当想到未来几年的造型时，我喜欢把造型想象成一种构图。也就是说，我们的[组件设计系统](https://blog.bitsrc.io/building-a-consistent-ui-design-system-4481fb37470f)应该包括逻辑组件和主题组件，它们可以使用像 [Bit](https://bit.dev) 这样的工具组合在一起。通过这种方式，你可以创建一个根据需要发展和变化的设计系统，而不会把一个笨重的库强加给不愿意采用它的开发人员。设计工具本身，如 Sketch an Figma，利用 will 组件实现这一目的(将它们与 Bit 结合，你就得到最终的组件设计系统。这太令人兴奋了。

*   [2019 年 React 组件风格的 5 种方式](https://blog.bitsrc.io/5-ways-to-style-react-components-in-2019-30f1ccc2b5b)
*   [构建 UI 组件设计系统](https://blog.bitsrc.io/building-a-consistent-ui-design-system-4481fb37470f)

### 7。面向数据驱动应用的 GraphQL API 客户端

因此，使用 GraphQL 通过组件为客户打开了令人兴奋的可能性。使用 [Apollo](https://github.com/apollographql/apollo-client) 你可以很容易地构建通过 GraphQL 获取数据的 UI 组件。结合 [Bit](https://github.com/teambit/bit) ，你可以从你正在工作的消费项目中导入和开发这些组件。

通过 API 的智能管理，我们可以简化围绕数据驱动的应用程序开发的工作流程，并加快我们工作的开发速度。因此，向前看几年绝对是值得的。

[2019 年你应该知道的 13 个 GraphQL 工具和库](https://blog.bitsrc.io/13-graphql-tools-and-libraries-you-should-know-in-2019-e4b9005f6fc2)

### 8。基于组件的设计工具

随着[组件成为我们的设计系统](https://blog.bitsrc.io/building-a-consistent-ui-design-system-4481fb37470f)，设计师和开发者之间的鸿沟将被弥合。从设计者和开发者的角度来看，这在等式的两端都是可能的。

[草图](https://medium.com/u/d39f69b23aa2)已经在设计组件之间创建了依赖链接，因此您可以以模块化的方式设计和更新设计。[代码组件的集成](https://github.com/airbnb/react-sketchapp)已经开始萌芽，这只是时间问题。像 [Figma](https://medium.com/u/bf1152b11387) 这样的工具是基于可重用的 UI 元素从头开始构建的。Framer 团队正在为编码设计者开发一个工具，在一定程度上可以控制将 UI 元素转化为可重用的 React 组件。通过 [Bit](https://bit.dev) ，你可以把自己设计的组件变成可重用的积木，可以在任何地方可视化地发现、使用甚至开发，从开发者端弥合了鸿沟。Bit +组件设计工具是一个强大的未来。通过 CDN 的位和 web 组件，这意味着全面的组合。

*   使用 React 和 Bit 的原子设计:简化复杂的用户界面
*   [优步设计|在优步了解设计](https://www.uber.design/case-studies/rebrand)
*   [构建视觉语言](https://airbnb.design/building-a-visual-language/)