# 构建免费代码并测试 ES 模块导入

> 原文：<https://dev.to/remy/build-free-code-testing-es-module-imports-31h0>

这个帖子的标题有点拗口，但我可以向未来的自己保证，这篇帖子值得发表，因为它将为未来的自己节省许多对构建工具大喊大叫的时间。

目标:没有构建工具，这导致没有配置，这导致没有等待。

## 让我们摆脱谎言吧

有一个小配置需要，但我可以向你保证，未来的雷米，这是一个复制和粘贴工作。

虽然我承认一些事情，但值得注意的是，我*认为*这种策略对分布式节点模块不起作用。也就是说:这个过程是为了应用开发和测试。

## TL；速度三角形定位法(dead reckoning)

安装几个位:

```
$ npm i esm
$ npm i -D ava nyc 
```

用我的[解决方案](https://remysharp.com/2019/08/16/build-free-code-and-testing-es-module-imports#final-solution)更新你的`package.json`，使用`import`语句，没有任何构建麻烦。

### 1。放电合成法（Electric Synthetic Method 的缩写）

它已经存在有一段时间了，但是当我最初尝试的时候，我并没有取得多大的成功。最近， [esm 模块](https://github.com/standard-things/esm)对我来说开箱即用。如果你不熟悉:

> 非常简单、无通天塔、无捆绑包的 ECMAScript 模块加载器。

这段代码将允许我随心所欲地命名我的文件——通常是`someProcess.js`(不是。mjs 或者。cjs，。watjs)。在这些文件中，我也可以使用`import`,就像我是来自未来的太空人一样。使用`import`将对我自己的文件和我安装的任何其他包起作用。

```
import fs from 'fs'; // node internals
import nodemon from 'nodemon'; // an "ES5-style" package made compatible
import { method } from './my-methods'; // my local code

export const number = 12;
export default () => {
  // do something magical
} 
```

要使用 esm，我可以将它包含在我的代码中…嗯，不，恶心，或者我可以让 node 预先加载 esm 模块，这样我的代码就不会看到库。在我的`package.json`文件中，我将有:

```
{
  "scripts": {
    "start": "node -r esm .",
    "dev": "DEBUG=* node -r esm ."
  },
  "main": "src/index.js"
} 
```

就是这样。接下来是测试，但这并不那么简单(首先！).

## 2。测试

我是 [tap](https://remysharp.com/2016/02/08/testing-tape-vs-tap) 的粉丝有一段时间了，我用摩卡(一个很老的版本)做 nodemon(为了我的罪过)，最近我喜欢用 Jest 做测试。

然而，Jest 不支持像 node 那样在运行时需要额外的模块。Mocha 和其他测试框架有，但不是玩笑。这有点讨厌，但它让我再次环顾四周。

我选定了 AVA 的 T2。我仍然不是很喜欢，但它正在做它的测试工作。重要的是，AVA 让我将 esm 作为测试的一部分，我可以在我的`package.json`(再次)中定义它，所以到目前为止所有这些东西都在一起:

```
{
  "ava": { "require": ["esm"] },
  "scripts": {
    "test": "ava",
    "start": "node -r esm .",
    "dev": "DEBUG=* node -r esm ."
  },
  "main": "src/index.js"
} 
```

再说一遍，就是这样。我的测试现在工作了，我的代码仍然使用`import`语句。没有构建过程(或者我知道的，这是我喜欢的方式)。

拼图游戏的最后一部分是覆盖率。我使用覆盖率作为我没有错过一些重要代码分叉的指示器(而不是以 100%覆盖率为目标)。AVA 让使用 [nyc](https://istanbul.js.org/) (“伊斯坦布尔命令行界面”…不管那是什么意思…)变得很容易。除了……T2 的保险不太管用。

## 3。新闻报道

我发现如果代码库使用了`require`语句，但是我的测试使用了`import`，那么覆盖率就会报告。如果是 100% `import`的陈述，我会面对精彩的一点废话:

```
----------|----------|----------|----------|----------|-------------------|
File | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s |
----------|----------|----------|----------|----------|-------------------|
All files | 0 | 0 | 0 | 0 | |
----------|----------|----------|----------|----------|-------------------| 
```

我试着手动调试，但完全没有用。感谢星星，当我看到 [Andre Torgal](https://andretorgal.com/) 的这个回购时，它给了我在纽约提取导入文件所需的大部分信息。

首先，esm 完全运行需要以下内容`"esm": { "cjs": true }`(我不完全确定为什么，但相信我经历了许多排列！).下一个纽约市*也需要*知道装入 esm 模块，所以最后的`package.json`读作:

```
{
  "esm": { "cjs": true },
  "nyc": { "require": ["esm"] },
  "ava": { "require": ["esm"] },
  "scripts": {
    "start": "node -r esm .",
    "dev": "DEBUG=* node -r esm .",
    "test": "nyc --reporter=text ava"
  },
  "main": "src/index.js"
} 
```

这有点重复，但是它有效，而且没有任何构建工具的诡计，这是我生活中真正想要的。

*原载于[雷米夏普的 b:log](https://remysharp.com/2019/08/16/build-free-code-and-testing-es-module-imports)T3】*