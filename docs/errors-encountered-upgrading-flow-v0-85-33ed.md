# 升级流 v0.85 时遇到错误

> 原文：<https://dev.to/tanhauhau/errors-encountered-upgrading-flow-v0-85-33ed>

## 背景

尽管看到 JS 社区[慢慢地](https://medium.com/entria/incremental-migration-to-typescript-on-a-flowtype-codebase-515f6490d92d) [从 Flow 向 TypeScript 转移](https://davidgomes.com/porting-30k-lines-of-code-from-flow-to-typescript/)， [Shopee Web 前端](https://careers.shopee.sg/jobs/?region_id=1&dept_id=109&name=web%20frontend&limit=20&offset=0)代码库仍然在很大程度上与 [Flow](https://flow.org/) 卡在一起。😨

沉寂一段时间后，Flow [又回来了，并致力于成为一个更高效、更可靠、更友好的工具。因此，我们决定给心流最后一次机会。然而，为了享受 Flow 最近几天所做的改进，我们必须将 Flow 从 0.83(自我们上次更新以来)升级到 v0.97(最新的日期)，这意味着我们需要将过去的 v0.85、](https://medium.com/flow-type/what-the-flow-team-has-been-up-to-54239c62004f)[这意味着地狱](https://github.com/facebook/flow/issues/7493)升级到广泛使用 react-redux 的[代码库](https://github.com/flow-typed/flow-typed/issues/2946)。

我们勇敢的开发者之一，高伟接受了这个挑战，并决定把我们带到流程的光明面。😅。她[在推特上](https://twitter.com/wgao19/status/1115969686758248448)写了一篇文章给[讲述她是如何修正过程中出现的错误的。](https://dev.wgao19.cc/2019-04-17__making-flow-happy-after-0.85/)

为了客观地看待这个问题，从 Flow 0.85 开始，Flow 要求为隐式注释提供[必需的注释。或者更通俗一点，如果你试图`export`从一个泛型类(例如:`class Foo<T>`)或者函数(例如:`function foo<T>(){}`)中创建一些东西，而没有显式的类型参数(例如:`new Foo<Type>()`或者`foo<Type>()`)，Flow 会给你一个错误。](https://medium.com/flow-type/asking-for-required-annotations-64d4f9c1edf8)

或者通俗地说，如果你的回购充满 HOCs 或`connect()`，你就完蛋了。所以，如果你是升级心流项目的勇敢者之一，一定要看看[高伟的博客](https://dev.wgao19.cc/2019-04-17__making-flow-happy-after-0.85/)。

## 明面上

在这篇文章中，我想列出我们遇到的一些讨论较少的障碍，最新流语法的工具，即`TypeParameterInstantiation`，例如:用类型参数`new Foo<Type>()`或`foo<Type>()`调用函数或实例化类。

## 巴别塔&埃斯林

我们必须将我们的 babel 升级到 [babel v7](https://babeljs.io/docs/en/v7-migration) 并将 babel-eslint(eslint 的 babel 包装器)升级到 [babel-eslint v9](https://github.com/babel/babel-eslint/releases/tag/v9.0.0) 来支持这种新语法。

你可以在我之前的文章中读到我们是如何意识到这种升级的需要的。

关于[@ babel/plugin-transform-flow-strip-types](https://babeljs.io/docs/en/babel-plugin-transform-flow-strip-types)，我们遇到了另一个有趣的 bug，你可以在[我的另一篇博文](https://lihautan.com/parsing-error-flow-type-parameter-instantiation/)中读到更多关于我们如何发现它的内容。

## 漂亮些

我们不得不将 prettier 升级到 [v1.16.0](https://prettier.io/blog/2019/01/20/1.16.0.html) ，并使用`prettier`的`babel-flow`解析器来解决解析更新的流语法时出现的语法歧义。用更简单的话来说，告诉漂亮一点的人

```
foobar<Type>(1) 
```

是*用参数 1 和类型调用 foobar，“类型”*，而不是:

```
foobar < Type > 1 
```

*是 foobar <式的结果，大于 1？*😂

你可以在[prettle 的博客文章](https://prettier.io/blog/2019/01/20/1.16.0.html#add-babel-flow-parser-5685-by-ikatyang)中了解更多信息。

## VSCode

流[懒惰模式](https://github.com/facebook/flow/commit/3c0a2bbd118206a0a73a1a4d18375122c4ae1955)从 v0.68 开始就有了，但是我们直到最近才通过 VSCode [享受到懒惰模式的好处。](https://github.com/flowtype/flow-for-vscode/commit/9c1440068f8faee95e487fc9f69b5f5ffed64bf1)

现在我们可以在我们的`.vscode/settings.json` :
中指定`lazyMode`

```
{  "flow.useLSP":  true,  "flow.lazyMode":  "ide"  } 
```

虽然[惰性模式](https://flow.org/en/docs/lang/lazy-modes/)减少了流进行类型检查的范围，但是我们遇到的一个棘手问题是，在再次返回有意义的流状态之前，要等待流进行重新检查。流团队在 [v92.0](https://github.com/facebook/flow/releases/tag/v0.92.0) 中做了一些优化，它说:

> 这个版本将 IDE 支持的生活质量改进的几个月的努力工作推向了高潮。期待你的请求更快，你的请求花费更少的时间。

根据发布说明，Flow 现在能够在重新检查的同时**提供类型定义，关于他们如何实现这一点的进一步细节，你可以阅读[的 Flow 博客](https://medium.com/flow-type/a-more-responsive-flow-1a8cb01aec11)**

## 结束语

最后，我们设法让 Flow 在 v0.97 中运行🎉。我们已经为 0.83 版糟糕的开发者体验奋斗了很长时间，希望 0.97 版不会让我们失望。

最后，一定要查看这个博客中的所有链接，它们链接到 Github 问题、提交、发布说明，谁知道它可能会让你经历一些意想不到的冒险呢？🤷‍

但是如果你像我一样懒，这里有一些链接，它们是我写这篇博文时的参考:

*   [博客:在流类型代码库上增量迁移到类型脚本](https://medium.com/entria/incremental-migration-to-typescript-on-a-flowtype-codebase-515f6490d92d)
*   [博客:将 30k 行代码从 Flow 移植到 TypeScript](https://davidgomes.com/porting-30k-lines-of-code-from-flow-to-typescript)
*   [问题:0.85 之后官方的 type connect(来自 flow-typed/react-redux)是什么方式？](https://github.com/facebook/flow/issues/7493)
*   [问题:[react-redux] libdef 与流 v0.85 #2946 不兼容](https://github.com/flow-typed/flow-typed/issues/2946)
*   [Tweet:用我们的代码库很难让 flow 愉快地超过 0.85...](https://twitter.com/wgao19/status/1115969686758248448)
*   [博客:0.85 后让流量快乐](https://dev.wgao19.cc/2019-04-17__making-flow-happy-after-0.85/)
*   [博客:要求必要的注释](https://medium.com/flow-type/asking-for-required-annotations-64d4f9c1edf8)
*   [文档:巴别塔 7 迁移](https://babeljs.io/docs/en/v7-migration)
*   [发布说明:babel-eslint v9.0.0](https://github.com/babel/babel-eslint/releases/tag/v9.0.0)
*   [Docs:@ babel/plugin-transform-flow-strip-types](https://babeljs.io/docs/en/babel-plugin-transform-flow-strip-types)
*   [发行说明:更漂亮的 v1.16.0](https://prettier.io/blog/2019/01/20/1.16.0.html)
*   [提交:流状态的惰性模式消息](https://github.com/facebook/flow/commit/3c0a2bbd118206a0a73a1a4d18375122c4ae1955)
*   提交:专长(lsp):增加支持流 lazyMode 的设置。
*   [文档:流惰性模式](https://flow.org/en/docs/lang/lazy-modes/)
*   [发布说明:流程 v0.92.0](https://github.com/facebook/flow/releases/tag/v0.92.0)
*   [博客:一个反应更迅速的信息流](https://medium.com/flow-type/a-more-responsive-flow-1a8cb01aec11)