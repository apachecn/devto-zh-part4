# 反应可访问性资源

> 原文：<https://dev.to/httpjunkie/react-accessibility-2d99>

React 为我们这些开箱即用的开发者做了很多事情，让我们的网站更容易访问，但我们仍然有责任确保我们创造的最终产品不会造成不可访问性。我不是这方面的专家，但昨晚我确实住在快捷假日酒店，我让我的[朗读 chrome 扩展](https://chrome.google.com/webstore/detail/read-aloud-a-text-to-spee/hdhinadidafjejdhmfkjgnolgimiaplp?hl=en)给我读了一篇我的一个同事写的文章:([开发者网页可访问性指南](https://www.telerik.com/blogs/web-accessibility-guidebook-for-developers))。

我是一名 20 年的 web 开发人员，对 React 非常了解，你可能会认为我通过代理了解了很多这方面的东西，但是在过去几周深入这个主题后，我意识到我对许多技术和大多数 arias 非常无知，这些技术和 arias 可以帮助我构建提供更大访问权限的应用程序和组件。在这个旅程中，我想了解更多信息的第一站是 React JS 文档。

## React 关于可访问性的文档

reactjs.org 可访问性文档应该是 React 开发者解决可访问性问题的第一站。虽然该文档中最重要的链接与 React 无关，但它们是像 Web AIM、WCAG 清单& A11y 项目清单这样的页面。关于 WAI-ARIA 的信息，他们在 JSX 的支持，以及其他帮助你编写语义 HTML 的资源，比如 MDN HTML elements reference。

ReactJS 文档在可访问性方面做得对的一些事情是，它们促使您使用语义 HTML，并尽可能接近 HTML 来编码，即使是最复杂的场景。React 有助于可访问性的伟大之处在于它专注于组件。这意味着即使是最复杂的小部件也可以分解成简单的组件，并且可以采用迭代方法来确保您不会忘记应用程序的可访问性。

既然我们已经解决了这个问题。让我们讨论一些额外的工具和组件，它们可以帮助您在 React 中构建更易访问的应用程序。

## React 中可访问的开发工具

React 中作为开发人员可以使用的最重要的工具之一是 [react-a11y](https://github.com/reactjs/react-a11y) 。Ryan Florence 是 React 应用程序访问的主要支持者，他通过像 [Reach UI](https://ui.reach.tech/) 这样的项目创建了这个应用程序，现在你可以通过结合运行 [eslint-plugin-jsx-a11y](https://github.com/evcohen/eslint-plugin-jsx-a11y) 和 Ryan 的 react-a11y 来 lint 你的应用程序。您可以查看，了解在 React 中开发时可以选择哪些警告和错误。这有助于确保您在开发过程中考虑可访问性。

我应该提到的一件事是，我在 React 文档中了解到, [Create React App](https://facebook.github.io/create-react-app/) 有这个插件，它带有一些在项目初始化时激活的规则。

## 可访问 React 组件

### React 文档标题

在你的应用程序中，你可以做的一件事就是始终确保你在管理你的文档标题，看看 Dan Abramov 的 [React 文档标题](https://github.com/gaearon/react-document-title)。这确保用户知道当前页面或 React route 的文档标题。

### 反应过来咏叹调现场

在下面的文章中( [ARIA live regions in React](https://almerosteyn.com/2017/09/aria-live-regions-in-react) )概述了我们理解使用 [ARIA Live Regions](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions) 为屏幕阅读器创建消息来创建实时公告的挑战。本文中涉及的棘手部分是，如果整个区域已经存在于 DOM 中并且已经发生了更改，那么对内容的更改只能由屏幕阅读器读出。在 React 中，我们需要确保在发送第一条消息之前呈现一个活动区域，并且它会一直呈现，直到不再需要它。幸运的是，这篇文章的作者[阿尔梅罗·斯泰恩](https://twitter.com/kryptos_rsa)已经创建了一个名为 [react-aria-live](https://github.com/AlmeroSteyn/react-aria-live) 的组件套件，你可以用 npm 安装它。

我建议你先通读这篇文章，然后再通读 GitHub 自述文件，以便更好地理解这个主题。

### 做出反应关注

WAI-ARIA 兼容的模态对话框或任何全屏任务的完整解决方案，我第一次了解到这个解决方案是通过深入研究 [react-focus-lock](https://github.com/theKashey/react-focus-lock) 来实现的，它设置了不同的隔离锁，并允许从一个到另一个的跳转。然后我意识到这只是一个更大的解决方案的一部分，该解决方案还使用[反应-移除-滚动](https://github.com/theKashey/react-remove-scroll)来禁用页面滚动和用户交互，并使用 [aria-hidden](https://github.com/theKashey/aria-hidden) 来对屏幕阅读器隐藏页面的其余部分。如果你正在使用这些组件，你可能已经购买了很大程度上使你的网站非常容易被屏幕读者访问，正如我们之前所说的，这样做的时间是在开发阶段，而不是作为一个附加的事后想法。查看使用所有这些组件的[现场演示](https://p3vjp8mzw7.codesandbox.io/)

### 到达 UI

Reach UI 是一套 UI 组件，它首先直接处理可访问性，事实上，Reach UI 网站上的文档在每个页面的顶部都引用了 W3.org·WAI-ARIA 的创作实践。主页上说它试图成为你的基于 React 的设计系统的可访问的基础，它还处于开发的早期阶段，所以还没有很多。我认为这有点谦虚，因为他们几乎有十个 UI 组件都是在考虑可访问性的基础上构建的。正如他们提到的，你来这里不是为了完全样式化和令人惊讶的复杂 UI 组件，相反，Ryan 希望你用他的库做的是把它作为你设计系统的起点。

### 剑术

因此，我对 [KendoReact](https://www.telerik.com/kendo-react-ui) 团队的组成以及他们致力于实现无障碍环境的努力印象深刻。主要是因为我们的组件非常复杂，所以我知道这对他们来说是持续的挑战，但他们对此非常重视。我们从零开始构建我们的组件，并牢记可访问性，您可以在我们的 [KendoReact 可访问性概述页面](https://www.telerik.com/kendo-react-ui/components/accessibility/)上找到我们在构建组件时考虑的所有措施。同样，我们也很想听听 React 社区关于我们可以做得更好以使我们的组件更易访问的任何事情。

## 播客

在我结束之前，我想给你们留下一些惊人的 React 和 Web 开发播客，它们涉及到可访问性的问题。我是一个超级播客迷，我可以列出一百多个关于这个话题的播客，但是我想给你们几个，帮助你们从一个更好的角度理解可访问性，就像从一个专家的角度。以及一些与应用程序开发直接相关的播客。

### React 播客

*   [接触亚伦·坎农](https://reactpodcast.simplecast.fm/54)
*   [只需使用 Jen Luker 的一个按钮](https://reactpodcast.simplecast.fm/34)
*   [瑞安·弗洛伦斯承诺无障碍](https://reactpodcast.simplecast.fm/22)
*   [与 Alex Reardon 一起进行快速、便捷、美观的拖放操作](https://reactpodcast.simplecast.fm/17)

### 做出反应综述

*   Leslie Cohn-Wein 认为可访问性不是一个“反应问题”

### 商店脱口秀

*   【Nicolas Steenhout 和 Christopher Schmitt 的可访问性
*   [Heydon Pickering 的可访问性](https://shoptalkshow.com/episodes/355/)
*   【Geri 科亚迪的色彩辅助功能

### 网络大秀

*   【Geri 科亚迪的彩色辅助功能工作流程
*   [可访问性并不是一件“值得拥有”的事情——德里克·费瑟斯通](http://5by5.tv/bigwebshow/184)

## 结论

就像这些工具或组件一样，没有什么是可访问性的灵丹妙药，事实上，我不确定是否有人已经建立了一个完全可访问的网站，总有一些障碍和条件会对人类使用计算机和网络应用程序构成挑战。但是通过采取这些措施并用屏幕阅读器测试你的网站，比如 NVDA 或者 T2 的 WAVE 评估工具，这样你就可以评估你网站的可访问性。

我希望在我当前的项目中使用这些工具和组件中的大部分，并在这个主题上写更多的东西，现在这是一个让我自己学习更多的练习，并看看我可以做些什么来改进我的应用程序，同时教别人。感谢您阅读或收听这篇文章，如果您有任何关于如何改进这一资源的想法，请留下评论并让我知道。我会修改并更新文章！