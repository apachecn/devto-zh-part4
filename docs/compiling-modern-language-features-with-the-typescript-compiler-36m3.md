# 用 TypeScript 编译器编译现代语言功能

> 原文：<https://dev.to/4nduril/compiling-modern-language-features-with-the-typescript-compiler-36m3>

## 前言

本文是“发布现代 JavaScript(或 TypeScript)库”系列的第 3 部分。查看简介中的动机和其他部分[的链接。](https://dev.to/4nduril/publish-a-modern-javascript-or-typescript-library-3hc3)

## 如何使用 TypeScript 编译器`tsc`来传输你的代码

**如果对设定背后的背景和推理不感兴趣，[直接跳到结论](#cmlfwttc-conclusion)T3】**

在上一篇文章中，我们设置了 Babel 来将现代 JavaScript 甚至是 TypeScript 转换成我们的目标浏览器能够理解的形式。但是我们也可以使用 TypeScript 编译器`tsc`来做这件事。为了便于说明，我用 TypeScript 重写了我的小型[示例库](https://github.com/4nduril/library-starter/tree/rewrite-in-typescript)。一定要查看其中一个前缀为`typescript-`的分支。`master`仍然是用 JavaScript 编写的。

我假设您已经知道如何设置 TypeScript 项目。否则你怎么能用 TS 编写你的库呢？相反，我将只关注为了交付一个库，transpiling 可能的最佳配置。

您已经知道，配置是通过项目根目录中的`tsconfig.json`完成的。它应该包含以下选项，我将在下面进一步讨论:

```
{
  "include": ["./src/**/*"],
  "compilerOptions": {
    "outDir": "./dist",
    "target": "es2017",
    "module": "esnext",
    "moduleResolution": "node",
    "importHelpers": true
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## `include`和`outDir`

这些选项告诉`tsc`在哪里找到要编译的文件，以及把结果放在哪里。当我们讨论如何将类型声明文件与代码一起发出时，`outDir`也将用于它们的目的地。

注意，这些选项允许我们只在命令行上运行`tsc`,而不需要其他任何东西，它会找到我们的文件，并把输出放到它应该在的地方。

## 目标环境

还记得我们在“巴别塔”文章中讨论`browserslist`的时候吗？(如果不是，[点击这里](https://dev.to/4nduril/transpile-modern-language-features-with-babel-4fcp)。)我们使用了一组查询来告诉 Babel 我们的代码应该能够在哪些环境中运行。而`tsc`则不然。

如果你感兴趣，请阅读 TypeScript GitHub 资源库中这篇有趣的[问题](https://github.com/Microsoft/TypeScript/issues/19183)。也许将来的某一天我们会在`tsc`中拥有这样的特性，但是现在，我们必须使用“JavaScript 版本”作为目标。

您可能知道，自 2015 年以来，TC39 委员会每年都会批准一个新版本的 ECMAScript，该版本包含所有在批准前已达到“完成”阶段的新功能。(参见[TC39 工艺](https://tc39.es/process-document/)。)

现在`tsc`允许我们(只)指定我们的目标 ECMAScript 版本。为了达到与巴贝尔和我固执己见的`browserslist`配置差不多的结果，我决定选择`es2017`。我已经使用了 [ECMAScript 兼容性表](https://kangax.github.io/compat-table/es2016plus/)，并检查了哪个版本可以“安全”地假设 Edge/Chrome/Firefox/Safari/iOS 的最后两个版本可以处理它。您的里程可能会有所不同！你基本上至少有三种选择:

*   按照我的建议，使用`es2017`。
*   根据兼容性表做出自己的决定。
*   选择最安全的选项并使用`es5`。这将产生也能在 Internet Explorer 11 中运行的代码，但它的大小会大得多——适用于所有浏览器。

就像我的`browserslist`配置一样，我将在以后的文章中讨论如何提供多个包:一个用于现代环境，一个用于旧环境。

这里需要注意的另一点是:`target`并没有直接设置在输出中应该使用哪个模块语法！你可能认为是这样的，因为如果你不显式设置`module`(见下一节)，`tsc`将根据你的`target`设置选择它。如果你的`target`是`es3`或者`es5`，`module`会被隐式设置为`CommonJS`。否则将设置为`es6`。为了确保您不会对`tsc`为您选择的内容感到惊讶，您应该始终按照下一节所述显式设置`module`。

## `module`和`moduleResolution`

将`module`设置为`"esnext"`与我们的`babel.config.js`中预置的`env`的`modules: false`选项大致相同:我们确保代码的模块语法保持为 ESModules，以启用树抖动。

如果我们设置了`module: "esnext"`，我们也必须将`moduleResolution`设置为`"node"`。TypeScript 编译器有两种查找非相关模块的模式(即`import {x} from 'moduleA'`与`import {y} from './moduleB'`)这两种模式分别叫做`node`和`classic`。前者的工作方式类似于 NodeJS 的解析模式(因此得名)。后者不知道`node_modules`这很奇怪，几乎从来不是你想要的。但是当`module`被设置为`"esnext"`时`tsc`使能`classic`模式，所以你必须明确地告诉它如何运行。

在上面的`target`部分，我提到过如果`target`不是`es3`或`es5`，那么`tsc`会将`module`隐式设置为`es6`。`es6`和`esnext`有细微的区别。根据[中的回答，这个 GitHub 问题](https://github.com/Microsoft/TypeScript/issues/24082) `esnext`是针对所有“在标准轨道上，但不在官方 es 规范中”(还没有)的特性。这包括像动态导入语法(`import()`)这样的特性，这肯定是您应该能够使用的，因为它支持使用 Webpack 进行代码拆分。(也许对应用程序来说比对库来说更重要一点，但是你要知道。)

## `importHelpers`

你可以把`importHelpers`比作 Babel 的`transform-runtime`插件:T2 现在向`tslib`注入导入，包含所有这些助手，就像`@babel/runtime`一样，而不是一次又一次地内联相同的助手函数，让你的库越来越大。但这次我们将安装生产依赖项，而不是留给我们的用户:

`npm i tslib`

原因是没有它,`tsc`将无法编译。`importHelpers`在我们的代码中创建导入，如果`tsc`没有找到被导入的模块，它会异常中止。

## 应该用`tsc`还是巴别塔来翻译？

这有点基于观点。但是我认为你和巴贝尔在一起比和`tsc`在一起更好。

TypeScript 很棒，可以有很多好处(即使我个人认为 JavaScript 作为一种语言，没有它会更强大，而且 TypeScript 带来的麻烦超过了它的好处)。如果你想，你应该使用它！但是让 Babel 生成您将要交付的最终 JavaScript 文件。Babel 允许更好的配置，并为此进行了高度优化。TypeScript 的目的是提供类型安全，因此您应该为此(单独)使用它。还有另一个问题:多填充物。

有了一个好的 Babel 设置，您就可以获得在目标环境中运行代码所需的一切。不是用`tsc`！现在，您的任务是提供代码所需的所有聚合填充。首先，要弄清楚这些是什么。即使你不同意我关于 Babel 和 TypeScript 的不同用例的观点，polyfill 问题本身应该足以让我理解这一点。

有一篇关于用巴别塔代替`tsc`进行 transpiling 的精彩博文:[用巴别塔打字稿:美好的婚姻](https://iamturns.com/typescript-babel/)。它还列出了对 ts 使用 Babel 的注意事项:在 TypeScript 中有四件小事是可能的，但 Babel 不能正确理解:名称空间(不要使用它们。它们已经过时了。)，用尖括号进行类型转换(改用`as`语法。)，`const enum`(省略`const`使用正常枚举。)和传统风格的导入/导出语法(这是**传统** —随它去)。我认为这里唯一重要的约束是`const enum`,因为如果使用标准枚举，它会导致输出中的代码多一点。但是除非你引入的枚举有成百上千的成员，否则这个问题可以忽略不计。

此外，丢弃所有类型注释比首先检查类型要快得多。例如，这使得在开发/监视模式下的编译周期更快。我在本系列中使用的示例项目可能做得不够，不能被视为一个好的编译时示例。但是在我的另一个由大约 25 个源文件和几个第三方依赖项组成的库项目中，Babel 比`tsc`快 5 倍。当你在编码时，每次保存后都要等待才能看到结果，这已经够烦人的了。

## `tsc`设置的结论和最终注释

(如果您真的想使用`tsc`来完成这个任务(参见上面的最后几段): )

安装`tslib`:

`npm i tslib`

确保您的`tsconfig.json`至少包含以下选项:

```
{
  "compilerOptions": {
    "outDir": "./dist", // where should tsc put the transpiled files
    "target": "es2017", // set of features that we assume our targets can handle themselves
    "module": "esnext", // emit ESModules to allow treeshaking
    "moduleResolution": "node", // necessary with 'module: esnext'
    "importHelpers": true // use tslib for helper deduplication
  },
  "include": ["./src/**/*"] // which files to compile
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你确定你想要或者需要支持像 Android/Samsung 4.4 或者 Internet Explorer 11 这样只有一种配置的老浏览器，用`es5`替换`es2017`目标。在以后的文章中，我将讨论如何创建和发布多个包:一个尽可能小，用于更现代的目标，另一个用更多的助手代码支持更老的引擎，因此更大。

记住:在这篇文章中，我只谈到了使用`tsc`作为 transpiler。我们当然会用它来进行类型检查，但这是另一个章节。

接下来:类型检查和提供类型声明

* * *

一如既往，非常感谢我的朋友 Tim Kraut 校对了这篇文章！