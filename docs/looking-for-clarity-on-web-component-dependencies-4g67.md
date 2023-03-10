# 寻找 web 组件依赖关系的清晰性

> 原文：<https://dev.to/schofeld/looking-for-clarity-on-web-component-dependencies-4g67>

我知道 web 组件的时代正在向我们走来，很多人对它们感到兴奋(T2)。

所以我想我最好振作起来，开始认真对待它们。

问题是，我长期以来一直支持杰里米·基思在他的“建设”演讲中阐述的那种网络开发方法，他在 1 月份的 [New Adventures 2019](https://newadventuresconf.com/2019/) 上发表了演讲。我当时不在现场，但他在 **notist** 上分享的 6 张幻灯片尤其吸引了我:准确地说是从 [34](https://speaking.adactio.com/87IIn1#sn3gBTn) 到 [39](https://speaking.adactio.com/87IIn1#sseICro) 的幻灯片。我发了关于他们的微博。

关于 Jeremy 的第 39 张幻灯片，重要的一点是 JavaScript 仍然是一种增强功能，而不是一种依赖。我知道这么说很老套，但我还是觉得 [@sil](https://twitter.com/sil) 的[人人都有 JavaScript 吧？](https://kryogenix.org/code/browser/everyonehasjs.html) **事项**。

考虑到这一点，我正在寻找 web 组件前景的清晰性。

我已经快速阅读了一些关于 web 组件的教程和介绍(但还没有完全理解)。我见过的所有人都只是简单地告诉用户代码是如何部署的，或者假设你知道所有这些东西。

MDN 上的[介绍将 web 组件描述为:](https://developer.mozilla.org/en-US/docs/Web/Web_Components)

> 一组 JavaScript APIs，允许您定义自定义元素及其行为，然后可以根据需要在您的用户界面中使用

我的理解是，如果 JS 不能在客户端运行，web 组件也不能。是这样吗？

换句话说，像我这样的老顽固是不是不得不接受完全依赖 JS 的世界就在眼前，我们最好还是接受它？

或者是不是可以有一个**创作**环境，然后将组件部署为常规的 HTML 和 CSS，而 JS 仍然是一个增强？

有什么地方让我忽略的环境问题变得清晰了吗？也许有一个描述了除了 Node 之外的开发工具包可以是什么的工具——对于像我这样仍然大量投资于 PHP/MySQL 框架的落后者来说？

希望有人能帮忙。