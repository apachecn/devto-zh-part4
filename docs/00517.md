# 为什么对 Typescript 大肆宣传？

> 原文：<https://dev.to/brieucp/why-so-much-hype-over-typescript-4p80>

## 有些前后关系

首先不要误会我，**类型很棒**。我每天在工作中使用 Typescript，这篇文章的目的不是质疑类型系统的好处。因为类型给出了上下文，使我们免于愚蠢的错误，并允许我们通过避免 js 解释器在 JIT 编译和运行时执行的大多数自动类型推断来避免性能问题。我认为任何人在编写 javascript 的时候都应该使用一个打字系统，如果他想分享它或者长期维护它的话。

但是，尽管我很欣赏 Typescript 作为类型系统的好处(即使我的几个同事使用了太多的**any**，以至于在应用程序的大多数地方，与纯 javascript 相比，它根本没有带来任何好处)。我对 Typescript 作为编译器持怀疑态度。以下所有的论证和思考都将 Typescript 视为编译器和 javascript 的超集，而不是增强 javascript 的类型系统。

## 它为最小的利益增加了许多复杂性

使用 typescript 时，你需要配置它，这并不简单。选项一点都不清楚，文档也很模糊。例如，问问自己*目标*和*模块*选项之间有什么不同。
好了，目标很简单*宠溺:不完全是*这是你的目标环境...
但是等等？！？ES2017 到底是什么？而 ES2018 呢？
让我们以 ES2017 为例:

*   [对象.值](https://caniuse.com/#search=Object.values) / [对象.条目](https://caniuse.com/#search=Object.entries)；
*   [字符串填充](https://caniuse.com/#search=String.prototype.padStart)；
*   [Object.getOwnPropertyDescriptors](https://caniuse.com/#search=getOwnPropertyDescriptors) ;
*   函数参数列表和调用中的尾随逗号；
*   和[异步功能](https://caniuse.com/#search=Async%20functions)。

好的，很好。但是哪些导航器支持这些特性呢？另一个问题，你知道 ES2017 是这一套功能，而没有检查我可以使用链接吗？我没有。

因此，使用目标选项，您必须知道您想要的特性来自哪个版本的 ECMAscript。然后，您必须检查您的目标浏览器是否支持该特性，或者(如果该特性是可聚合的)编写代码并处理膨胀的代码。如果您查看了“我可以使用吗”链接，您应该也会注意到，即使所有这些功能都是 ES2017 的一部分。对于浏览器版本来说没有任何意义。因为 web 浏览器独立实现 ecmascript 功能。如果你正在使用 Babel，你可以使用@babel/preset-env 并使用 [browserslist](https://github.com/browserslist/browserslist) 通过**有意义的**查询精确定位你想要的浏览器(注意:你仍然应该对产品膨胀保持警惕[，但是你可以更加自信，至少知道你提供给用户的代码是有用的)](https://medium.com/@WebReflection/avoiding-babels-production-bloat-d53eea2e1cbf)

还有一个事实是编译器选项包括关于缩小的选项。这可以通过 typescript 目标是完全处理捆绑过程的事实来解释。但事实并非如此。在大多数情况下，您仍然需要添加一个真正模块捆绑器到您的构建链中，以便能够使用 typescript 做一些真正的事情。

最后，typecript 编译器有一些限制。例如，如果您想要使用动态导入，您必须使用*模块:“es next”*，如果您的目标是 ES2015，即使您的捆绑包(网络包或包裹)支持它，您也不能使用该模块。这意味着您不能在针对传统浏览器时拆分代码...

## Typescript 不再是 javascript 的超集

纯 javascript 和 typescript 之间有一些区别。例如，在 typescript 中的@ decorators 和 ECMAScript 中当前正在规范化的内容之间。这可能会导致在运行时使用它们时出现一些差异。

除此之外，还可以编写 **today** [有效代码，由 typescript 和 javascript](https://medium.com/@jasvir/monoglots-when-a-subset-is-not-1604e3a51d9) 解析器进行不同的解析，这将导致不同的运行时执行。我知道，我知道，你不太可能遇到像这样的用例。但这并不能阻止现实。

## 用 JSDoc 代替

正如我之前所说，我仍然认为我们应该键入我们的代码。事实上，即使没有打字稿也很容易做到。[我们只需要在普通的 javascript 文件](https://www.typescriptlang.org/docs/handbook/type-checking-javascript-files.html)中编写 JSDoc 声明。

然后，我们可以使用 typescript 通过- CheckJs 选项对它们进行类型检查。通过在参数中启用 Check JS，你还可以设置 VS 代码(可能还有大部分的文本编辑器和 IDE)来显示 JS 文件的类型检查。

使用 JSDoc 而不是 typescript 一个很大的优点是您编写的 javascript 不需要任何编译步骤。如果你愿意，你仍然可以使用 babel，但是你也可以用 Javascript。

关于 JSDoc 的一些资源:

*   [用 JSDoc](https://medium.com/@trukrs/type-safe-javascript-with-jsdoc-7a2a63209b76) 键入安全的 JavaScript
*   [JSDoc.app](https://jsdoc.app/)

* * *

现在，我不是要你放弃打字稿。如果你喜欢，就坚持下去。我只是想知道为什么每个人都加入了 typescript 的行列，而在大多数情况下，与注释中包含的一些常规类型相比，我看到的更多的是约束而不是好处。

* * *

PS:我没有讲 tslint vs eslint [是因为大家都认同 typescript](https://eslint.org/blog/2019/01/future-typescript-eslint) 的优越性。既然我谈到了 browserify，我也必须谈谈我最喜欢的一个 eslint 插件，它是

PPS:英语不是我的母语，所以不要犹豫，纠正任何事情。我用了一个校正器，但它可能并不完美。