# 使用 JavaScript 框架有好处

> 原文：<https://dev.to/marcush/using-javascript-framework-powers-for-good-ncg>

*最初发布于 [marcus.io](https://marcus.io/blog/using-framework-powers-for-good) 。*

通常，当谈到现代 JavaScript 框架和可访问性时，主流观点是“React 和 company 不利于包容性 web”。但在我看来，不应该责怪框架——你可以编写可访问或不可访问的代码，就像你在普通、陈旧、无工具链和无组件的 HTML 中会(或不会)做的那样。相反，最终导致无法访问应用的是教育和框架生态系统:首先，专注于功能而不是 HTML/CSS 基础的训练营和培训。其次，教程中不可访问的代码和整体缺乏意识(诚然，这对于 web 平台整体来说也是成立的)。

但是这篇文章并不是关于现代 JavaScript 框架的真实的或可感知的问题——而是关于为了可访问性而使用它们的优势、它们独特的特性或架构方法。[本周，我在 twitter](https://twitter.com/_marcusherrmann/status/1159330247403298816) 上询问了关于如何使用框架做好事的想法，可以这么说:React、Vue、Angular 和 Co 以及它们的特性实际上以什么方式促进了包容性设计？

> 让我们把 WebApp/React/A11y 的讨论转向:你知道框架特征、基于组件的架构、它们的能力等等的例子吗？实际上有助于#a11y，或者至少有助于构建有思想和包容性的应用程序？

## 有*有*的优势

我得到了很好的回复。以下是我总结、归纳和解释答案的尝试:

### 开发者体验

让可访问的解决方案成为开发人员体验的一部分——这意味着:让事情变得简单，用起来有趣。利用架构上的优势，一切(包括可访问性规范的助手或 linters)都在`npm install`之外。

### 外部:可用，内部:可访问

有了理想的组件，所有的可访问性功能都得到了照顾，可以说都经过了实战考验——但都藏在他们的“引擎室”里。可以说，开发人员只是使用了组件接口。[或者像 Hidde de Vries 所说的](https://twitter.com/hdv/status/1159514406423859200):将组件的内部从其使用层中分离出来[在他相应的博客文章“烘焙组件的可访问性”中有更多的描述](https://hiddedevries.nl/en/blog/2019-05-24-baking-accessibility-into-components-how-frameworks-help)

### 使可及性难以规避

让使用不仅仅是一种乐趣，还要确保它实际上是一种让实现变得不可访问的努力。他联系[星巴克的 React 模式库](https://www.starbucks.com/developer/pattern-library/components/forms/)作为例子。

Perry Trinier 写道，基于组件的架构可以通过设置相关的必需属性类型(例如，输入标签、字段集的图例)来帮助我们实现可访问性特性

#### 使用框架的所有功能...

你的框架有一些特性旨在让你的开发者生活变得更容易，例如像 Vue 中的 mixins 或 React 中的 hooks 这样的概念。 [Marcy Sutton](https://twitter.com/marcysutton/status/1159336829084004352) 、 [James Nimlos](https://twitter.com/JamesNimlos/status/1159487023347974145) 和 [Almero Steyn](https://twitter.com/kryptos_rsa/status/1159367612792483841) 建议使用这些来管理 ARIA 状态和属性值，例如为 ARIA 引用生成的唯一 id(`aria-labelledby`、`aria-describedby`、`aria-controls`等)，或者输入的显式标签(这个概念:`<label for="foo">Name:</label><input id="foo" />`—“foo”是生成的 id)。

### ...和它的设计方法

在他的推文中，[@ giuseppegurgonee 提到](https://twitter.com/giuseppegurgone/status/1159548580241268736)你可以使用框架的声明性来获得可访问性特性。Guiseppe 还写了一篇详细的博客文章来解释他的意思:[“用 React 构建可访问的模态”](https://pspdfkit.com/blog/2018/building-accessible-modals-with-react/)。

### 自动化...

Almero Steyn 提醒我们:我们可以让我们的框架和我们不仅编写标记，而且实际上正在编程的事实来帮助我们完成文档大纲:[例如，通过 tenon UI](https://twitter.com/kryptos_rsa/status/1159465365233111040) 的标题组件来自动化标题级别(想法来自[索菲·艾伯特](https://twitter.com/sophiebits)

### ...或者聪明地使用道具

如果你不想完全自动化，你仍然可以使用 props 将适当的标题级别传递给你的组件，正如 Heydon Pickering 在去年的[“管理设计系统中的标题级别”](https://medium.com/@Heydon/managing-heading-levels-in-design-systems-18be9a746fa3)中所写的

### (app)故事的两面

@ accessibly 喜欢框架提供“在服务器和客户端同时拥有(大部分)相同代码的可能性”。这为逐步提高提供了机会。”

## 结论(暂时)

再次感谢所有受访者！总之，这些回复是更深入研究的一个很好的起点，坦率地说，是真正鼓舞人心的东西。激励——一方面，更深入地钻研框架的高级概念，但另一方面，激励检查所有未来特性是否适合改进可访问性。因此，希望在未来的几个月或几年中,“框架独有的可访问性优势”的列表会越来越多！