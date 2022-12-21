# 普通打字稿(gts)

> 原文：<https://dev.to/rfornal/vanilla-typescript-gts-10bd>

我最近开始了一个新的个人项目。起初，开发进展顺利。然后，我意识到我真的需要 TypeScript 提供的类型检查。在过去从事打字工作之后，我知道自己想要什么...问题是如何到达那里。

使用包含 TypeScript 的框架很容易使用它。但是，如果一个简单的 TypeScript 项目只是一个普通的 JavaScript 项目呢？

在我早期的研究中，我遇到了 Google TypeScript Style (gts)。

> gts 是 Google 的 TypeScript 风格指南，也是我们的格式化程序、linter 和自动代码修复程序的配置。没有 lint 规则要编辑，没有配置要更新，没有语法上的自行车脱落。

## 自行车脱落？

**名词**
[自行车脱落](https://en.wiktionary.org/wiki/bikeshedding)

1.  在无关紧要的技术问题上浪费时间和精力。
2.  拖延症。

## 什么是 gts？

### 从他们的文献记载:[这里](https://github.com/google/gts)。

gts 是 Google 的 TypeScript 风格指南，也是我们的格式化程序、linter 和自动代码修复程序的配置。没有 lint 规则要编辑，没有配置要更新，没有语法上的自行车脱落。

借用标准 j:

*   **无配置**。在项目中实施一致风格的最简单方法。把它扔进去。
*   **自动格式化代码**。只需运行 gts fix，告别杂乱或不一致的代码。
*   **抓住风格问题&程序员早期错误**。通过消除评审者&贡献者之间的来回，节省宝贵的代码评审时间。
*   **固执己见，但不至于过了头**。我们建议您使用默认配置，但是如果您需要定制编译器或 linter 配置，您可以这样做。

在幕后，我们使用 tslint 来加强样式指南并提供自动修复，以及更漂亮地重新格式化代码。

### 这是什么意思

对我来说，这意味着我可以开始一个预配置的**固执己见的** TypeScript 项目，可能需要一些小的调整，但基本上已经准备好了。

此外，我认为指出该文档在几个方面是不正确的(在我看来是可以接受的)是有好处的，并且在页面底部有一段我差点错过的引文...

> 注意:这不是谷歌的官方产品。

## 我的 gts 经历...

安装非常简单...

```
$ npx gts init 
```

Enter fullscreen mode Exit fullscreen mode

...它用谷歌的模式安装了以下内容...

*   package.json
*   tsconfig.json
*   林顿. json
*   prettle . config . js

gts 文档还说“如果一个源文件夹不存在，它将添加一个默认的模板项目。”在我的例子中，没有安装模板项目；这很好，因为我正在将另一个项目转移到这个领域。

然后，我从以前的普通 JavaScript 项目中复制了我的代码，并创建了一个 ***。ts*** 文件。这是事情变得更有趣的地方。我将详述几个问题...大多数问题都与从这一点转换到 TypeScript 有关。

### 打字稿问题...

***console.log*** 在我的代码中抛出一个错误(找不到)...根据快速搜索，我跑了...

```
$ npm install @types/node 
```

Enter fullscreen mode Exit fullscreen mode

同样，当我在浏览器中使用 ***窗口*** 引用全局对象的元素时，我得到了一个错误...之前看过这个，我用的是 ***( <任意>窗口)*** 。

此外，我对 ***package.json*** 进行了调整，加入了一个构建过程，以便更好地与我的项目保持一致，并处理了一些将 JavaScript 转换为 TypeScript 的其他问题。

## 结论

当我第一次开始这个项目并意识到我想使用 TypeScript 向前发展时，我进行了快速搜索，发现[在 alligator.io 上建立了一个新的 TypeScript 项目](https://alligator.io/typescript/new-project/),这是一篇很棒的文章，但是我不想在每次我想进入一些包含 TypeScript 的小项目时重复所有这些步骤。

但是等等...文章的最后是[让事情变得更简单:输入 gts](https://alligator.io/typescript/new-project/#making-things-even-easier-enter-gts) 。

从所有这些中，我发现了一种模式，它工作得相当好，不需要所有疯狂的步骤就可以让一个普通的 TypeScript 项目运行起来！

## 图像(封面)

来自 www.flaticon.com[的](https://www.flaticon.com/) [Freepik](https://www.freepik.com/) 制作的图标由授权给 [CC 3.0](http://creativecommons.org/licenses/by/3.0/)