# 我可以将 ES6 发布到 npm 吗？

> 原文：<https://dev.to/stereobooster/can-i-publish-es6-to-npm-5hfk>

最近我为了好玩写了一个[小库](https://github.com/stereobooster/css-modules-components)，它为 CSS 模块实现了[样式组件模式](https://dev.to/stereobooster/styled-components-one-more-time-5g0l)。我进退两难:我应该如何发布它，我应该把它编译成 ES5 和 CJS 或 UMD 版本，还是一次发布多个版本，用什么工具来发布它。之前我试过`kcd-scripts`(肯特·c·多兹)和`microbundle`(杰森·米勒)。

这一次，我认为这将是一个玩具项目的麻烦。所以我继续做下去，[将它发布为 ES6](https://unpkg.com/css-modules-components@1.0.1/index.js) (没有缩小或任何东西的源代码)，**，但是**具有节点样式的模块解析。

严格地说，由于节点样式的模块解析，它不能被浏览器使用。它不能由节点使用，因为 ES6 模块的节点支持在标志后面。它只能由捆绑商使用，如 webpack(例如 CRA)和 package(实际上我没有测试过)。

以我为例，我有外部依赖(React)，但比如 [polished 就没有外部依赖](https://github.com/styled-components/polished/issues/442)。

**发布 ES6 有什么不好的地方？**你的方法是什么？告诉我你的想法

> 照片由 chuttersnap 在 Unsplash 上拍摄