# 俄罗斯方块:选择工具和建立项目

> 原文：<https://dev.to/sleeplessbyte/tetris-choosing-the-tools-and-setting-up-the-project-4kh6>

> 🔙这是使用 React 构建俄罗斯方块克隆系列的第二部分。如果你错过了第一个，在这里找到它[。](https://dev.to/sleeplessbyte/tetris-building-a-game-using-javascript-3j6f)

今天我们将朝着开始这个项目迈出一步。我将讨论您在引导自己的项目时可能会遇到的各种选项和选择。谈论这些很重要——特别是因为很多教程和指南完全跳过了*为什么*——你会注意到，并不是所有的事情都非常清楚，都有一个前进的方向。

> 🎮在这个系列中，我将向你展示构建俄罗斯方块克隆的所有步骤，遵守[俄罗斯方块指南](https://tetris.fandom.com/wiki/Tetris_Guideline)，这是[俄罗斯方块公司](https://tetris.fandom.com/wiki/The_Tetris_Company)为制作所有新的(2001 年及以后)*俄罗斯方块*游戏产品在形式上相似而强制执行的当前规范。
> 
> 🛑 *俄罗斯方块*是有许可的，这意味着如果你打算用这一系列的文章来建造你自己的街机拼图，确保遵守法律，如果你打算商业发行它。即使你免费提供了一个克隆，你仍然可以得到一个停止和终止。[这个 reddit 帖子](https://www.reddit.com/r/gamedev/comments/5kkk82/law_concerns_when_creating_a_game_inspired_by/)非常全面地介绍了如何去做这件事。此外，[这篇 Ars Technica 文章](https://arstechnica.com/gaming/2012/06/defining-tetris-how-courts-judge-gaming-clones/)以*俄罗斯方块*和所谓的克隆*宋旻浩*为例，深入探讨了法庭如何评判游戏克隆体。
> 
> 📚这个系列纯粹是一个教育，非商业资源。我们将仅使用 *fandom wiki* 作为资源，并且仅使用俄罗斯方块这个名称来表示游戏的*类型，而不是实际的公司、游戏或品牌。*

[![Photo showing various machines in the Arcade "Lightroom", in New Brighton, Wallasey, United Kingdom](img/ae87a369791f52607e9f562de58c3622.png "Photo by Carl Raw (https://unsplash.com/@carltraw) on Unsplash (https://unsplash.com/)")](https://res.cloudinary.com/practicaldev/image/fetch/s--dJcgWzhE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kmddjw275q1tzeym5f74.jpg)

## 目录

*   [目录](#table-of-contents)
*   [游戏引擎](#the-game-engine)
*   [工具链](#the-toolchain)
    *   [套餐管理](#package-management)
    *   [捆扎机](#bundler)
    *   [编译器](#compiler)
    *   [林挺和样式导轨](#linting-and-styleguides)
    *   [测试库](#testing-libraries)
    *   [基础库](#base-Library)
    *   [自举](#bootstrap)
*   [项目初始化](#initialisation-of-the-project)
*   [正确设置`typescript`](#setting-up-raw-typescript-endraw-correctly)
*   [正确设置`babel`](#setting-up-raw-babel-endraw-correctly)
*   [正确设置`eslint`](#setting-up-raw-eslint-endraw-correctly)
    *   [测试`eslint`配置](#testing-the-raw-eslint-endraw-configuration)
    *   [微调规则](#fine-tuning-the-rules)
*   [正确设置`jest`](#setting-up-raw-jest-endraw-correctly)
    *   [启用`jest-dom`扩展](#enabling-raw-jest-dom-endraw-extensions)
    *   [获取覆盖报告](#getting-a-coverage-report)
*   [正确设置`prettier`](#setting-up-raw-prettier-endraw-correctly)
    *   [自动格式化](#automatically-formatting)
*   [结论](#conclusion)

## 游戏引擎

因为这个系列有一个游戏作为它的交付物，所以选择一个游戏引擎是明智的。从维基百科文章中可以看出，游戏引擎是一个软件开发环境，为人们构建视频游戏而设计。有一个完整的[游戏引擎列表](https://en.wikipedia.org/wiki/List_of_game_engines)，它并不完整，选择哪一个用于你的游戏是如此的努力，以至于[许多](https://www.youtube.com/watch?v=ibK3Ds7nDyk) [有](https://www.youtube.com/watch?v=2tZK75R2K2c) [完整的](https://www.linkedin.com/pulse/how-choose-game-engine-yann-kronberg/) [文章](https://www.technobyte.org/which-game-engine-should-you-choose/) [或](https://blackshellmedia.com/2016/09/29/6-crucial-questions-ask-choosing-game-engine/) [视频](https://www.youtube.com/watch?v=Tnci1hO0prc) [关于它](http://mightyfingers.com/blog/11-steps-for-choosing-the-best-game-engine/)。在我看来，如果你正在从头开始开发一款游戏，并且你有时间、潜力和选择，你只需要问自己以下问题:

1.  我想玩多人游戏吗？挑[虚幻引擎](https://www.unrealengine.com/en-US/)。
2.  我想建一个第一人称射击游戏吗(单人还是多人)？挑[虚幻引擎](https://www.unrealengine.com/en-US/)。
3.  否则，选择 [Unity](https://unity.com/) 。

我是根据一小时又一小时的 GDC 会谈和工作列表得出这个结论的！还有许多更有趣的引擎，但是如果你需要其他人*信任*和*能够使用的东西，很快*，你可能需要从这两个中选择一个。

如果你是一个人的商店，并且为网络而建，有一个 javascript 游戏引擎的[集合，包括众所周知的选项，如](https://github.com/collections/javascript-game-engines) [GameMaker Studio (2)](https://www.yoyogames.com/gamemaker) 。

*然而*，既然这个系列是用 *react* 建造一个俄罗斯方块克隆体，那就是**正是我要用的**。问问你自己:React 是这项工作的合适工具吗？咩，大概不会(因为有*更好的*工具。仅仅因为你能让某样东西工作，并不意味着它是正确的选择。这重要吗？这取决于和你一起工作的人以及围绕抽象和挑战工作的意愿。

## 工具链

因为`react`应该用于这个项目，所以这个项目很可能会被构建为一个 JavaScript 应用程序。JavaScript 项目(和库)往往有一个(子)工具集，我称之为“工具链”。

### 套餐管理

包管理器有它自己的功能:它管理包。在您的包*清单*中列出的 JavaScript 模块(项目所依赖的包的集合，例如列出 URL 或名称，以及版本或版本范围)是您的项目的依赖项。目前比较流行的有[纱](https://yarnpkg.com/en/)和 [NPM](https://www.npmjs.com/) 。

您可能会问:“但是我不总是需要一个包管理器吗？”答案是简短的**不**。您还可以选择:

*   在本地包含所有的依赖项，例如通过*出售*(将依赖项存储到项目本地的行为)它们。这意味着你*总是*有一个工作副本，不需要网络。
*   使用一个不使用传统意义上的包的运行时，比如 [deno](https://deno.land/) ，但是也使用 [unpkg](https://unpkg.com/) ，这使得你的 HTML 文件集*依赖清单*和*管理器*于一身。
*   使用系统包，如`.deb` ian 包，并使用系统工具，如`make`和 Makefile 来管理依赖关系。这在技术上仍然使用*包管理器*，但是与`Yarn`或`npm`选项的方式不同。

> 💎我选择了包清单在`package.json`中的`Yarn`。我认为有很多合理的理由来选择上面的任何一个选项。我自己已经使用了其中的大部分，包括现在已经废弃的工具，如[鲍尔](https://bower.io/)。这通常并不重要，应该根据公司政策或项目团队的调整来做出决定。

### 捆绑者

JavaScript 生态系统中的捆绑器不要与 Ruby 生态系统中的*包管理器* [捆绑器](https://bundler.io)混淆。在 JavaScript 生态系统中，它通常负责以下一组特性或其中的一个子集:

*   收集项目中的所有资源(JS、HTML、文件、图像、CSS)
*   剥离未使用的资产(想想树摇动、死代码/导入消除)
*   应用转换(transpilation，例如 Babel，post processing，例如 PostCSS)
*   输出代码包(组块、代码分割、缓存友好输出)
*   错误记录(更友好)
*   热模块替换(在开发期间自动更新模块/资产)

我过去用过并且现在还在使用的一些工具有 [Webpack](https://webpack.js.org/) 、 [Parcel](https://parceljs.org/) 、 [Rollup](https://rollupjs.org/guide/en/) 、 [microbundle](https://www.npmjs.com/package/microbundle) 、 [Browserify](http://browserify.org/) 和 [Brunch](https://brunch.io/) 。同样的*可以通过使用 [Grunt](https://gruntjs.com/) 之类的任务运行器或者使用 [Gulp](https://gulpjs.com/) 来实现*，但是根据我的经验，这些往往会很快失控。

这里的选择并不重要。我认为他们都有自己的优点和缺点，你应该选择你觉得舒服的。如果你预见到你需要定制很多东西，有些会比其他的更好。如果你的团队比其他人更了解他们中的一个，那可能是有利的。总的来说:**一个伟大的捆绑者是可以替换的**。

> 💎我还没有选择任何东西！我将让工具链的其余部分来决定我想使用哪种捆扎机。如果处理得当，以后更换捆扎机的成本不会很高。也许我将不再需要*任何*。

### 编译器

从技术上来说，babel 主要是一个 *transpiler* ，因为它将代码编译到相同的抽象层次(想想 JavaScript ES 和 JavaScript ES3)。一个*编译器*通常编译代码到一个较低的抽象层次(想想 Java 到 JVM /字节码，TypeScript 到 JavaScript)。也就是说， [Babel](https://babeljs.io/) 将自己列为一个编译器，因为它可以从类型脚本代码中移除类型脚本标记，生成有效的 JavaScript

> 💎由于我想要一些类型安全，并且我更擅长使用[类型脚本](https://www.typescriptlang.org/)(它确实附带了一个*编译器*，它也有*传输文件*)而不是[流](https://flow.org/)(它在技术上是一个静态类型检查器，而不是*编译器*或*传输器*)，我选择类型脚本用于*编译*进行类型检查，然后使用`babel`实际编译并传输文件

### 林挺和 Styleguides

根据[维基百科](https://en.wikipedia.org/wiki/Lint_(software))的说法，Lint 或 linter 是一种分析源代码以标记编程错误、bug、风格错误和可疑结构的工具。既然我将使用`TypeScript`，我至少在寻找一个代码换行器。

> 💎 [TSLint](https://palantir.github.io/tslint/) 已经被[弃用](https://medium.com/palantir/tslint-in-2019-1a144c2317a9)，因为微软推出了一个惊人的工具集，使 [ESLint](https://eslint.org/) 能够支持 Typescript，称为 [`typescript-eslint`](https://typescript-eslint.io/) 。

我也认为挑选一个编码风格指南(例如，你是否使用分号)并将其应用到项目中是一个很好的实践。为了实现这个目标，我将使用`prettier`。

### 测试库

好吧，这个也不是开创性的。虽然这里有很多选择，比如[摩卡](https://mochajs.org/)、[茉莉](https://jasmine.github.io/)、[磁带](https://github.com/substack/tape)，或者我最喜欢的 [AVA](https://github.com/avajs/ava) ，我还是用 [jest](https://jestjs.io/) 。我个人认为它拥有我喜欢的 AVA 的所有伟大特性，但是因为脸书在内部使用它，有相当多的 *React 工具*完美地挂钩到`jest`。

### 基地库

当你想在“反应”中发展时，目前有多种选择:

*   `react`:[https://reactjs.org/](https://reactjs.org/)
*   `preact`:[https://preactjs.com/](https://preactjs.com/)
*   `react-native-web`:[https://github.com/necolas/react-native-web](https://github.com/necolas/react-native-web)

> 💎不去深究细节，因为这个系列专注于用`react`写一个游戏，那就是我要用的；如果在这个系列的结尾有很多问题，我会写一篇关于如何从`react`转移到`preact`的文章。

### 自举

如果你读过 [react 文档](https://reactjs.org/docs/create-a-new-react-app.html)，你可能会知道有几个“工具链”存在。它们主要是提供单一命令行界面(CLI)的包装器，并与所有的依赖项(工具)捆绑在一起，如上面各种类别中所列。React 团队主要推荐了几个解决方案，我倾向于同意它们:

*   如果你正在学习 React 或创建一个新的单页应用程序，使用[创建 React 应用程序](https://github.com/facebook/create-react-app)。
*   如果你正在用 Node.js 构建一个服务器渲染的网站，试试 [Next.js](https://nextjs.org/) 。
*   如果你正在建立一个静态的面向内容的网站，试试 [Gatsby](https://www.gatsbyjs.org/) 。
*   如果你正在构建一个组件库或者集成一个现有的代码库，试试[中微子](https://neutrinojs.org/)、 [nwb](https://github.com/insin/nwb) 、[包裹](https://parceljs.org/)或者 [Razzle](https://github.com/jaredpalmer/razzle) 。

我想在组合中加入 [`react-static`](https://react-static.js.org) ，以及`next.js`和`gatsby`的替代品，它允许你建立超级快速的静态内容网站，用 react-app 补充水分，不需要使用`GraphQL`或服务器。

这是一个非常重要的决定，因为如果你选择使用上面工具链之一的 bootstrapped 项目，你将在某种程度上被他们的技术、配置选择和一般想法所束缚。大多数工具都允许你*弹出*(停止使用内置默认)，但是你仍然需要做很多工作才能移开。

> 💎该项目(俄罗斯方块克隆)可能是完全可行的，没有一个完整的自举工具链。这就是为什么我选择了*而不是*来使用一个引导工具链。当我需要添加一些它目前不能正确支持的东西时，当我试图升级依赖项或类似的东西时，我经常会遇到 *meh* 行为。如果我最终需要它，我可以在以后添加它！

## 项目的初始化

```
# Create the directory for this new project
mkdir tetreact

# Move into that directory
cd tetreact

# Install dependencies
yarn add react react-dom

# Install development dependencies (explanation below)
yarn add typescript core-js@3 eslint eslint-config-prettier eslint-plugin-import -D
yarn add eslint-plugin-jsx-a11y eslint-plugin-react eslint-plugin-react-hooks -D
yarn add jest babel-jest prettier @babel/cli @babel/core @babel/preset-env -D
yarn add @babel/preset-react @babel/preset-typescript @typescript-eslint/eslint-plugin -D
yarn add @typescript-eslint/parser @testing-library/react @testing-library/jest-dom -D
yarn add @types/jest @types/react @types/react-dom -D

# Make this a git repository
git init 
```

以下是安装以下软件包的原因:

*   `react`和`react-dom`是 react 的运行时包，
*   `typescript`:用于对`ts`和`tsx`文件进行类型检查，
*   `core-js`:poly fills 特色库。有一个旧版本(`@2`)和一个新版本(`@3`)。
*   `eslint`:棉绒芯包，
*   `eslint-config-prettier`:关闭有冲突的风格规则，
*   `eslint-plugin-import`:增加`import`和`export`语句的规则和林挺。
*   `eslint-plugin-jsx-a11y`:增加 JSX 元素的可访问性规则，
*   `eslint-plugin-react`:增加了 React 特定的林挺规则，
*   `eslint-plugin-react-hooks`:增加了 React 钩子特有的林挺规则，
*   `jest`:测试框架，
*   `babel-jest`:可以通过巴别塔运行测试代码*，*
*   允许我从命令行运行 babel 作为一个独立的命令，
*   `@babel/core`:巴别塔的核心包，
*   `@babel/preset-env`:预设，根据浏览器列表，确定需要对代码应用哪些转换，
*   `@babel/preset-react`:允许 JSX 转换并确保 React 的功能组件属性`displayName`设置正确的预置。
*   `@babel/preset-typescript`:允许从文件中剥离 TypeScript 类型的标记，留下有效的 JavaScript，
*   为林挺打字稿增加了很多规则，
*   `@typescript-eslint/parser`:允许`eslint`使用 TypeScript ESLint 解析器(知道类型标记)，
*   `@testing-library/react`:增加官方推荐测试库，针对 react，
*   `@testing-library/jest-dom`:为`jest`和 DOM 添加特殊匹配器，
*   `@types/*`:类型定义

您可能会想:“哎呀，这有很多依赖项”，是的，有很多。然而，当使用类似于`create-react-app`的东西时，如果没有更多的依赖项，你将安装同样的*，因为这些是你将依赖的`react-scripts`项目的依赖项。我花了相当多的时间来整理这个列表，但是你可以随意修改和/或添加。*

通常我会在进行过程中添加这些依赖项，但我已经完成了下面列出的所有步骤，所以我收集了所有的依赖项，并在两个命令中列出它们，供您复制和粘贴。

## 正确设置`typescript`

下面是设置`typescript`。为此添加的依赖项有:

*   `typescript`:提供`tsc` typescript 编译器并允许您拥有一个项目版本，不同于例如与您的 IDE 或文本编辑器捆绑的版本。

运行`tsc --init`命令，用默认设置创建`tsconfig.json`。

```
yarn tsc --init 
```

现在我需要做一些改动，所有改动都在下面解释:

```
-  // "incremental": true, +  "incremental": true -  // "target": "es5", +  "target": "esnext", -  // "jsx": "preserve", +  "jsx": "preserve", -  // "noEmit": true, +  "noEmit": true, -  // "isolatedModules": true, +  "isolatedModules": true, -  // "moduleResolution": "node", +  "moduleResolution": "node", -  // "allowSyntheticDefaultImports": true, +  "allowSyntheticDefaultImports": true, 
```

记住，目标是让`tsc`对代码库进行类型检查。这意味着不需要输出，因此有了`noEmit`。此外，它不需要花时间转换到一个旧的 JavaScript，因为`babel`会处理，这意味着它可以有一个`esnext`目标。同理，`jsx`设置为`preserve`，而**不是** `react`。巴贝尔会处理的。然后有几个选项使得与其他包的互操作性更容易。最后，`isolatedModules`是 TypeScript over Babel 功能正确工作所必需的。

此外，`package.json`需要用运行类型检查的命令获得`"scripts"`键。

```
+  "scripts": {
+    "lint:types": "yarn tsc"
+  } 
```

运行`yarn lint:types`应该会产生以下错误:

```
error TS18003: No inputs were found in config file 'path/to/tetreact/tsconfig.json'. Specified
'include' paths were '["**/*"]' and 'exclude' paths were '[]'.

Found 1 error. 
```

这是正确的错误。没什么好编的！我们来补充一下:

```
mkdir src
touch src/App.tsx 
```

运行`yarn lint:types`应该会产生以下错误:

```
node_modules/@types/babel__template/index.d.ts:16:28 - error TS2583: Cannot find name 'Set'. Do
you need to change your target library? Try changing the `lib` compiler option to es2015 or later.

16     placeholderWhitelist?: Set<string>;
                              ~~~

node_modules/@types/react/index.d.ts:377:23 - error TS2583: Cannot find name 'Set'. Do you need
to change your target library? Try changing the `lib` compiler option to es2015 or later.

377         interactions: Set<SchedulerInteraction>,
                          ~~~

src/App.tsx:1:1 - error TS1208: All files must be modules when the '--isolatedModules' flag is
provided.

1 
```

让我们从前两个开始。这些给出了修复错误的明确选项。

```
-  // "lib": [], +  "lib": ["dom", "es2015"], 
```

这与在您的`.eslintrc`配置文件中设置正确的`env`非常相似:我需要告诉 TypeScript 我在一个浏览器环境(`dom`)中，并且它应该能够访问那些已经在`es2015`中引入的构造。

最后一个错误是因为`--isolatedModules`标志。当用这个标志/选项运行编译器时，每个文件*都希望*成为它自己的独立模块。一个文件仅仅是一个模块，如果它是什么东西的话。这个标志的原因并不明显:它在`@babel/plugin-transform-typescript` 的[文档中被列为使用 Babel“编译”TypeScript 的警告之一。我在这里有先进的知识，但它会在下一步变得清晰。](https://babeljs.io/docs/en/babel-plugin-transform-typescript)

我更新了`src/App.tsx`文件:

```
import React from 'react'

export function App(): JSX.Element {
  return <div>Hello world</div>
} 
```

最后，`tsc`*不抱怨*。

## 正确设置`babel`

下一步是确保`babel`“编译”类型脚本代码为 JavaScript，应用转换并挂钩到我已经安装的各种插件中。

*   `core-js@3`:poly fills 特色库。有较老的版本(`@2`)和较新的版本(`@3`)；它由`@babel/preset-env`结合`browerlist`配置使用，
*   允许我从命令行运行 babel 作为一个独立的命令，
*   `@babel/core`:巴别塔的核心包，
*   `@babel/preset-env`:预设，根据浏览器列表，确定需要对代码应用哪些转换，
*   `@babel/preset-react`:允许 JSX 转换并确保 React 的功能组件属性`displayName`设置正确的预置。
*   `@babel/preset-typescript`:允许从文件中剥离类型脚本类型标记，留下有效的 JavaScript。

目前，在撰写本文的时候，Babel 还没有一个`--init`命令，但是设置它并不复杂，尽管需要一些努力才能正确列出所有的预置和插件。由于这是一个*项目*，根据[通天塔文档](https://babeljs.io/docs/en/configuration#what-s-your-use-case)，这个项目的最佳方式是创建一个`JSON`配置，称为`.babelrc`。

```
touch .babelrc 
```

内容如下，是我取了三个`@babel/preset-*`插件的文档并应用后收集的:

```
{  "presets":  [  [  "@babel/preset-env",  {  "targets":  {  "node":  "current"  },  "useBuiltIns":  "usage",  "corejs":  {  "version":  3  }  }  ],  "@babel/preset-typescript",  "@babel/preset-react"  ],  "ignore":  [  "node_modules",  "dist"  ]  } 
```

显式定义`browserlists`键/配置也是一个好主意，尽管因为我正在构建一个跨环境跨浏览器游戏，所以设置可以保留在`defaults`上。为了做到这一点，为了让阿贝尔使用`@babel/cli`来调用`babel`，在`package.json`中，我添加了以下内容:

```
 {
     "scripts": {
+      "build": "yarn babel src --out-dir dist --extensions \".ts,.tsx\"",
+      "watch": "yarn build --watch",
       "lint:types": "yarn tsc"
     },
     "dependencies": {

  ...

       "typescript": "^3.5.3"
     },
+    "browserslist": [
+      "defaults"
+    ]
   } 
```

如果您想要不同的目标，请确保遵循[浏览器列表最佳实践](https://github.com/browserslist/browserslist#best-practices)。您也可以使用配置文件；挑你喜欢的。

让我们看看这是否有效！

```
$ yarn build
yarn run v1.16.0
warning package.json: No license field
$ babel src --out-dir dist --extensions ".ts,.tsx"
Successfully compiled 1 file with Babel.
Done in 1.67s. 
```

在`dist`中，我现在可以找到`App.js`，它没有任何类型信息。它应该是这样的:

```
"use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});
exports.App = App;

var _react = _interopRequireDefault(require("react"));

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

function App() {
  return _react.default.createElement("div", null, "Hello World!");
} 
```

需要注意的几件事:

*   它补充了`"use strict";`
*   它使用`interopRequireDefault`来要求`react`的默认导出
*   使用`_react.default.createElement`时发生了`JSX`

这三件事只有在 Babel 配置正确的情况下才会发生。

## 正确设置`eslint`

下一步是确保 TypeScript 代码可以被链接！

*   `eslint`:棉绒芯包，
*   `eslint-config-prettier`:关闭有冲突的风格规则，
*   `eslint-plugin-import`:增加`import`和`export`语句的规则和林挺。
*   `eslint-plugin-jsx-a11y`:增加 JSX 元素的可访问性规则，
*   `eslint-plugin-react`:增加了 React 特定的林挺规则，
*   `eslint-plugin-react-hooks`:增加了 React 钩子特有的林挺规则，
*   为林挺打字稿增加了很多规则，
*   `@typescript-eslint/parser`:允许`eslint`使用 TypeScript ESLint 解析器(它知道类型标记)。

`eslint`核心包附带一个 CLI 工具来初始化(并运行)`eslint` :

```
$ yarn eslint --init

? How would you like to use ESLint? To check syntax and find problems
? What type of modules does your project use? JavaScript modules (import/export)
? Which framework does your project use? React
? Where does your code run? Browser
? What format do you want your config file to be in? JSON

Successfully created .eslintrc.json file in path/to/tetreact 
```

根据您的配置，如果您调用`yarn eslint`(从本地`node_modules`执行`eslint`或普通`eslint`(可能调用“全局”安装的`eslint`)，可能会出现以下消息*:* 

```
The config that you've selected requires the following dependencies:

eslint-plugin-react@latest

? Would you like to install them now with npm? No 
```

我选择`"No"`是因为一方面，它已经安装在`devDependencies`下，另一方面，如果我说`"yes"`，它会试图使用`npm`来安装它，这是我不想要的(因为我正在使用`yarn`)。

至于选项:我个人喜欢`.json`文件，因为它限制了我使用`JavaScript`解决一些事情，这使得“黑客”做一些事情的障碍更高了。我基本上防止自己尝试做一些不受开箱即用支持的事情。您的里程数可能会有所不同，但我喜欢使用标准配置的依赖项，因为这样更容易搜索解决方案*和*请求支持！

> 🛑如果你遇到一个类似这样的错误:
> 
> ```
> ESLint couldn't find the plugin "eslint-plugin-react". This can happen for a
> couple different reasons:
> ... 
> ```
> 
> 即使*是本地安装的*，也要从全局包中删除`eslint`:
> 
> ```
> yarn global remove eslint 
> ```

如果你使用一个带有`eslint`集成设置的 IDE，那么`App.js`(在`dist`文件夹中)和`App.tsx`(在`src`文件夹中)都有可能出现错误。**这是意料之中的**。它不会用你的`devDependencies`中的所有插件自动配置`.eslintrc.json`。

为了获得所有的配置，我编辑了生成的`.eslintrc.json`。

*   首先，我将配置标记为`root`配置。这禁止树中任何地方的任何`eslint`配置将规则应用到这个项目。
*   接下来，我更新了`parserOptions`并告诉它使用`@typescript-eslint/parser`解析器。我的[文章](https://dev.to/xpbytes/writing-a-code-analyzer-in-typescript-5ec3)关于[编写一个类型脚本代码分析器](https://dev.to/xpbytes/writing-a-code-analyzer-in-typescript-5ec3)更详细地介绍了不同的`@typescript-eslint/*`包是什么和做什么的。
*   最后还有所有的`extends`。这些采用了我想要应用于该配置的预设配置。`@typescript-eslint/*`和`prettier/*`模块有文档说明*应该按照什么顺序*放置。

```
 {
+    "root": true,
+    "parser": "@typescript-eslint/parser",
     "parserOptions": {
+      "project": "./tsconfig.json",
+      "ecmaFeatures": {
+        "jsx": true
+      },
       "ecmaVersion": 2018,
       "sourceType": "module"
     },
     "env": {
       "browser": true,
       "es6": true
     },
-    "extends": "eslint:recommended" +    "extends": [
+      "eslint:recommended",
+      "plugin:@typescript-eslint/eslint-recommended",
+      "plugin:@typescript-eslint/recommended"
+      "plugin:react/recommended",
+      "prettier",
+      "prettier/@typescript-eslint",
+      "prettier/babel",
+      "prettier/react"
+    ],
     "globals": {
       "Atomics": "readonly",
       "SharedArrayBuffer": "readonly"
     },
     "plugins": [
-      "react", +      "@typescript-eslint",
+      "react-hooks",
     ],
     "rules": {
     },
+    "settings": {
+      "react": {
+        "version": "detect"
+      }
+    }
   } 
```

这个`rules`目前仍然是空的，我会说的。首先，我们来测试一下配置！

### 测试`eslint`配置

我改变`src/App.tsx` :

```
+  function Header() {
+    return <h1>Hello World!</h1>
+  } 
   export function App(): JSX.Element {
-    return <div>Hello World!</div> +    return <Header />
   } 
```

并添加新的`scripts`条目:

```
 "scripts" {
     "build": "yarn babel src --out-dir dist --extensions \".ts,.tsx\"",
      "watch": "yarn build --watch",
+     "lint": "yarn eslint src/**/*",
      "lint:types": "yarn tsc"
   }, 
```

现在由我来经营！

```
yarn lint

$ eslint src/**/*

path/to/tetreact/src/App.tsx
  3:1  warning  Missing return type on function  @typescript-eslint/explicit-function-return-type

✖ 1 problem (0 errors, 1 warning)

Done in 4.01s. 
```

伍普迪多。来自`@typescript-eslint`插件的警告！这正是我期望看到的，所以我现在可以继续微调`"rules"`。

### 微调规则

通常，当我开发一个库或项目时，我会微调`"rules"`,或者我使用一组由项目领导预先确定的规则。在[exercisem/JavaScript-analyzer 库](https://github.com/exercism/javascript-analyzer)中，我已经[添加了一个关于规则的文档](https://github.com/exercism/javascript-analyzer/blob/b32e6dc67842c4edad6f75e9b8909bf5d2ac6926/docs/linting.md)以及我为什么选择它们这样。结果如下所示，包括底部的两个`react-hooks`规则。

```
{  "rules":  {  "@typescript-eslint/explicit-function-return-type":  [  "warn",  {  "allowExpressions":  false,  "allowTypedFunctionExpressions":  true,  "allowHigherOrderFunctions":  true  }  ],  "@typescript-eslint/explicit-member-accessibility":  [  "warn",  {  "accessibility":  "no-public",  "overrides":  {  "accessors":  "explicit",  "constructors":  "no-public",  "methods":  "explicit",  "properties":  "explicit",  "parameterProperties":  "off"  }  }  ],  "@typescript-eslint/indent":  ["error",  2],  "@typescript-eslint/no-non-null-assertion":  "off",  "@typescript-eslint/no-parameter-properties":  [  "warn",  {  "allows":  [  "private",  "protected",  "public",  "private readonly",  "protected readonly",  "public readonly"  ]  }  ],  "@typescript-eslint/no-unused-vars":  "off",  "@typescript-eslint/no-use-before-define":  [  "error",  {  "functions":  false,  "typedefs":  false  }  ],  "react-hooks/rules-of-hooks":  "error",  "react-hooks/exhaustive-deps":  "warn"  }  } 
```

随着我编写更多的代码，这个规则集可能会改变，但是现在应该足够了。

## 正确设置`jest`

下一步是确保代码是可测试的。

我个人*不喜欢*把我的测试文件放在我的源文件旁边，而是把所有的测试放在一个单独的目录中。然而，这并不是更好或首选，只是不同。你喜欢做什么就做什么。如果你把测试放在一起，确保你的测试以`.test.ts`或`.test.tsx`结束，如果你没有，默认的*文件夹是`__tests__`。您可以在即将生成的`jest.config.js`中更改这些。*

重要的依赖关系是:

*   `jest`:测试框架，
*   `babel-jest`:可以通过巴别塔运行测试代码*，*
*   `@testing-library/react`:增加官方推荐测试库，针对 react，
*   `@testing-library/jest-dom`:为`jest`和 DOM 添加特殊匹配器，

就像其他一些工具一样，`jest`带有一个 CLI 和一个选项，允许您*生成*配置文件。

```
$ yarn jest --init

√ Would you like to use Jest when running "test" script in "package.json"? ... yes
√ Choose the test environment that will be used for testing » jsdom (browser-like)
√ Do you want Jest to add coverage reports? ... yes
√ Automatically clear mock calls and instances between every test? ... no 
```

这会将`test`脚本添加到`package.json`中的`"scripts"`中，并将默认的`jest.config.js`文件添加到根目录中。
配置文件的内容都设置正确(给出上面列出的答案)，重要的是(你可以进去确认):

*   `coverageDirectory`应该设置为`"coverage"`，因为我想要覆盖率报告，
*   `testEnvironment`不应该设置或设置为`"jest-environment-jsdom"`，因为我不想*有*在浏览器中运行。

`babel-jest`包是*自动*支持的，开箱即用，不需要设置任何其他东西。因为 Babel 已经被正确地配置为“编译”源代码，并且测试代码具有相同的属性，所以不需要采取任何步骤来使测试也被“编译”。

然后我想与`@testing-library/react`库集成，它提供了一个清理脚本，确保在每次测试后`React`应用程序的状态和环境被重置(清理)。它可以通过`jest.config.js`文件
进行设置，而不是包含在每个测试中

```
-  // setupFilesAfterEnv: [] +  setupFilesAfterEnv: [
+    '@testing-library/react/cleanup-after-each'
+  ], 
```

我在测试中使用默认的文件夹名:

```
mkdir __tests__ 
```

现在我用下面的代码创建一个冒烟测试`__tests__/App.tsx`:

```
import React from 'react'
import { render } from '@testing-library/react'
import { App } from '../src/App';

it('App renders heading', () => {
  const {queryByText} = render(
    <App />,
  );

  expect(queryByText(/Hi/)).toBeTruthy();
}); 
```

最后，我使用由`yarn jest --init` :
添加的`"scripts"`命令运行测试

```
yarn test

$ jest
 FAIL  __tests__/App.tsx
  × App renders heading (29ms)

  ● App renders heading

    expect(received).toBeTruthy()

    Received: null

      14 |   );
      15 |
    > 16 |   expect(queryByText(/Hi/)).toBeTruthy();
         |                             ^
      17 | });
      18 |

      at Object.toBeTruthy (__tests__/App.tsx:16:29)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        4.361s
Ran all test suites. 
```

啊。我在渲染`Hello World`，而不是`Hi`。所以现在我将正则表达式改为测试`Hello World`，并再次运行测试:

```
$ jest
 PASS  __tests__/App.tsx
  √ App renders heading (21ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        4.184s
Ran all test suites.
Done in 6.10s. 
```

### 启用`jest-dom`扩展

你可能已经注意到了另一个依赖关系。我想使用`'@testing-library/jest-dom/extend-expect'`可见性检查`toBeVisible`，而不是只通过`toBeTruthy`测试它是否存在。为了与那个包集成，我对`jest.config.js` :
做了如下修改

```
 setupFilesAfterEnv: [
     '@testing-library/react/cleanup-after-each',
+    '@testing-library/jest-dom/extend-expect',
   ], 
```

这一改变使得扩展(新的匹配器，包括`.toBeVisible`)可用于所有的测试。

我更新测试来使用这些:

```
 import React from 'react'
   import { render } from '@testing-library/react'
   import { App } from '../src/App'

   it('App renders heading', () => {
     const { container, queryByText } = render(
       <App />,
     );

-    expect(queryByText(/Hello World/)).toBeTruthy()
+    expect(queryByText(/Hello World/)).toBeVisible()
   } 
```

运行测试工作正常，但是我的 IDE 在`toBeVisible`匹配器上给出了一个错误。这是因为 TypeScript 并不知道`expect`匹配器已经被扩展了。它不擅长从*动态执行的代码*中推断出新的类型。由于在`jest`配置和这个测试之间没有*交叉文件*信息，我不能期望它被神奇地捡起来。幸运的是，有各种方法可以解决这个问题，例如，但不限于:

*   将`import '@testing-library/jest-dom/extend-expect'`添加到每个测试文件中。这扩展了`expect()`匹配器以包含库提供的那些匹配器，
*   确保`typescript`知道这总是包括在内的(这是真的，考虑到`jest.config.js`的变化)。

为了获得“总是被包含”的体验，我添加了一个新文件`declarations.d.ts`和一个三斜线指令*。我通常会避开这些指令，甚至有一个`eslint`规则来禁止它们，但是根据我的经验，当你遇到这样的问题并使用它们时，工具是最好的。如果你在未来的某个时候关注这篇文章，这可能不是真的。你可以做任何工作，也许一个`import`就够了:* 

```
touch __tests__/declarations.d.ts 
```

```
/* eslint-disable @typescript-eslint/no-triple-slash-reference */
/// <reference types="@testing-library/jest-dom/extend-expect" /> 
```

这是告诉 TypeScript，对于当前的目录子树(`__tests__`)，它应该总是添加指令定义的包类型。我现在还可以看到`__tests__/App.tsx`中的错误已经被解决，它可以识别`.toBeVisible`。

### 获取覆盖报告

覆盖率报告不需要新的依赖项，因为`jest`与内置覆盖率捆绑在一起。

为了测试`coverage`是否正常工作*，我首先修改了`App.tsx` src 文件以包含一个分支:* 

```
import React from 'react'

export interface AppProps {
  headingText?: string
}

export function App({ headingText }: AppProps): JSX.Element | null {
  if (headingText === undefined) {
    return null
  }

  return <h1>{headingText}</h1>
} 
```

现在，应用程序渲染`null`，除非给出一个`headingText`。我还必须更改测试以通过作为标题文本的`"Hello World"`，否则测试将
失败:

```
-  <App /> +  <App headingText="Hello World" />, 
```

我在启用覆盖率的情况下运行测试套件:

```
yarn test --coverage 
```

这将运行测试，并且测试结果是*通过*；它还输出以下表格摘要:

```
----------|----------|----------|----------|----------|-------------------|
File      |  % Stmts | % Branch |  % Funcs |  % Lines | Uncovered Line #s |
----------|----------|----------|----------|----------|-------------------|
All files |    66.67 |       50 |      100 |    66.67 |                   |
 App.tsx  |    66.67 |       50 |      100 |    66.67 |                 9 |
----------|----------|----------|----------|----------|-------------------| 
```

**第 9 行**在一个条件分支内(用于当`headerText === undefined` ):

```
 return null 
```

这可以通过显式添加一个测试来测试。

```
it('App renders nothing without headingText', () => {
  const { container } = render(
    <App />,
  )

  expect(container.firstChild).toBeNull()
}) 
```

我通常不喜欢测试事物是在*而不是*那里，因为通常你不得不做出一些最脆弱的假设(因此很容易被打破)，但只是为了测试`jest`是否已经被正确设置，这很好，因为我稍后会扔掉这些行:

```
$ jest --coverage
 PASS  __tests__/App.tsx
  √ App renders heading (46ms)
  √ App renders nothing without headingText (1ms)

----------|----------|----------|----------|----------|-------------------|
File      |  % Stmts | % Branch |  % Funcs |  % Lines | Uncovered Line #s |
----------|----------|----------|----------|----------|-------------------|
All files |      100 |      100 |      100 |      100 |                   |
 App.tsx  |      100 |      100 |      100 |      100 |                   |
----------|----------|----------|----------|----------|-------------------|
Test Suites: 1 passed, 1 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        4.911s
Ran all test suites.
Done in 6.78s. 
```

## 正确设置`prettier`

最后，我可以专注于设置(自动)代码格式化程序！我真的很喜欢`prettier`，原因很简单，它消除了讨论许多风格选择的需要。我*不*认为它总是或者甚至经常生成**漂亮的**代码，不过没关系。随着他们的库的改进，输出也在改进，一旦他们改进了，重新格式化所有的代码是很容易的。

*   `eslint-config-prettier`:关闭与更漂亮相冲突的样式规则。你可以在上面的`eslint`配置中看到各种`prettier/*`线路。这已经设置好了。
*   核心包，包括运行更漂亮的 CLI 工具。

更漂亮的已经被添加到`eslint`配置中，所以这部分可以跳过。

在编写时，`prettier` CLI 没有`--init`选项，所以我手工创建配置文件:

```
touch .prettierrc.json 
```

我选择了看似跟随`StandardJS`的风格，但这真的没关系。选择一种风格并坚持下去。

```
{  "trailingComma":  "es5",  "tabWidth":  2,  "semi":  false,  "singleQuote":  true,  "jsxSingleQuote":  false  } 
```

我还希望能够将这些作为脚本运行，所以我添加了以下三个`"scripts"` :

```
 "lint:types": "yarn tsc",
+  "lint:format": "yarn format --check",
+  "format": "yarn prettier \"{src,__{tests}__}/**/*.{ts,tsx}\"",
+  "format:fix": "yarn format --write",
   "test": "yarn jest" 
```

### 自动格式化

由于`prettier`已经作为插件添加到`eslint`中，它已经正确地与`eslint`集成。然而，您可能希望代码在保存时被格式化*。更漂亮的文档列出了许多 ide，并允许你在保存时打开格式化。*

总的来说，我不喜欢在提交时运行得更漂亮*，因为它会降低我的提交速度，偶尔会出错，我认为格式化代码不应该是提交的问题。也就是说，我确实认为在持续集成(CI)中添加一个检查来测试项目的格式是一个好主意。*

## 结论

就是这样！该项目现在处于开始编写代码的良好状态。是的，这花了不少时间，上面的许多配置设置正是诸如`create-react-app`甚至`parcel`捆绑器这样的工具存在的原因。注意，我实际上还没有处理过`parcel`和`webpack`处理的一些事情，比如导入图像或者其他文件类型；我不认为我会需要它，因此我没有添加它。

还有几件事要做:

*   设置配置项，
*   添加`"name"`和“许可证”字段，
*   添加 *servability* ，即添加我们可以在浏览器中看到的 HTML 文件。

下次我会*实际上*写一些游戏代码，也许是刚刚列出的东西，但是现在，这是我给你的全部。

[![Photo of "De Rotterdam", Rotterdam, Netherlands, with the building, the Cruise Terminal, and a small boat coasting towards the photographer on a gloomy day.](img/da9b1992591695f3c2b6d70201161ff6.png "Photo by Reginar (https://unsplash.com/@reginar) on Unsplash (https://unsplash.com/)")](https://res.cloudinary.com/practicaldev/image/fetch/s--if3Wbs4n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/74bosly3ryp7snoz4xt4.jpg)**