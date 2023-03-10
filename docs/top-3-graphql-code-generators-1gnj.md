# 前 3 名 GraphQL 代码生成器

> 原文：<https://dev.to/graphqleditor/top-3-graphql-code-generators-1gnj>

最近在 web 开发界围绕 GraphQL 有很多讨论，这是有充分理由的！

[GraphQL](https://graphql.org/) 是一种新的 API 查询语言，也是一种服务器端运行时，通过使用您为数据定义的**类型系统**来执行查询，所以基本上是一种描述如何请求数据并将其从服务器加载到客户端的语法。

## 为什么大家都在议论纷纷？

答案很简单:

> 都是关于**发展速度**的。

公司开始使用 GraphQL 的主要原因是它有望加速你的 API 开发，更重要的是，它能够交付！

在针对从 1 到 100 多名工程师(其中我们可以找到优步或 Paypal 这样的科技巨头)的各种团队的首席技术官和首席开发人员的调查中，几乎所有公司都认为 GraphQL 达到了他们的预期。

让我向您介绍三个有用的 GrapphQL 代码生成器，以加速您的开发:

### 1。GraphQL 宙斯

> *[宙斯](https://github.com/graphql-editor/graphql-zeus)，克罗诺斯之子，[阿波罗](https://www.apollographql.com/)之父。*

在 [Node.js](https://nodejs.org/en/) &浏览器中工作的简单工具，用于为 Javascript 或 Typescript 生成自动完成的客户端库。GraphQL Zeus 的独特之处在于，它创建了自己的规范以及类型安全的完成请求&响应。

[![graphql-zeus in action](img/a15b9cb32dc6a742df7c50f3e73f2f3c.png)](https://github.com/graphql-editor/graphql-zeus)

回购:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[graph QL-编辑](https://github.com/graphql-editor)/[graph QL-宙斯](https://github.com/graphql-editor/graphql-zeus)

### GraphQL 客户端和带有 GraphQL 自动完成库生成⚡⚡⚡的 GraphQL 代码生成器，用于浏览器、nodejs 和 react native

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/543fac0f547aab3b2e87c1d18d59e305.png)](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/mastimg/zeus.gif)

[![npm](img/5921bfe8ea491f9a92573ebbc8f31950.png)](https://www.npmjs.com/package/graphql-zeus)[![Commitizen friendly](img/f1a839b39b2a3c7fda72b1889e79f407.png)](http://commitizen.github.io/cz-cli/)[![npm downloads](img/3ffa84faea6f34a1c27f0819d010732d.png)](https://www.npmjs.com/package/graphql-zeus)

GraphQL Zeus 为`JavaScript`或`TypeScript`创建自动完成客户端库，为强类型查询提供自动完成功能。

从宙斯 2.0 版本开始<g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a1.png">⚡</g-emoji>T2】⚡T4】⚡支持映射类型！！！

支持的语言:

*   java 描述语言
    *   浏览器
    *   NodeJS
    *   反应自然
*   以打字打的文件
    *   浏览器
    *   NodeJS
    *   反应自然

## 它是如何工作的

给定以下模式[奥林巴斯卡](https://app.graphqleditor.com/a-team/olympus)

[![](img/1fd642e584424dfd68b6f980a5c83dd1.png)](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/mastimg/zeusexample.gif)

## 目录

*   [工作原理](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#how-it-works)
*   [目录](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#table-of-contents)
*   [执照](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#license)
*   [如何使用](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#how-to-use)
    *   [作为 CLI](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#as-a-cli)
        *   [安装](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#installation)
        *   [与 JavaScript 一起使用](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#usage-with-javascript)
        *   [与打字稿一起使用](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#usage-with-typescript)
        *   [与 NodeJS 一起使用](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#usage-with-nodejs)
        *   [与 React Native 一起使用](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#usage-with-react-native)
        *   [从 URL 加载](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#load-from-url)
    *   [使用生成的客户端示例](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#use-generated-client-example)
        *   [用链执行查询](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#perform-query-with-chain)
        *   [使用 Thunder 抽象的 Fetch 函数执行查询](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#perform-query-with-thunder---abstracted-fetch-function)
        *   [工会](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#unions)
        *   [接口](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#interfaces)
        *   [用别名执行查询](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#perform-query-with-aliases)
        *   [变量](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#variables)
        *   [Gql 字符串](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#gql-string)
        *   [JavaScript 类型转换](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#javascript-type-casting)
        *   [打字稿选择开始](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#typescript-selectionset)
    *   [规格](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#spec)
        *   [使用别名规范](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#use-alias-spec)
    *   [在你的项目中使用生成代码](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#use-in-your-project-to-generate-code)
    *   [在项目中使用以动态获取模式](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#use-in-your-project-to-dynamically-fetch-schema)
*   [支持](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#support)
*   [投稿](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#contribute)
*   [解析](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/master/#parsing)

## 许可证

用它

## 如何使用

graphql zeus 的主要用途应该是作为 CLI。

### 如同

…</article>

[View on GitHub](https://github.com/graphql-editor/graphql-zeus)

### 2。Prisma GraphQLGen

Prisma team 的工具提供了一个类型安全的环境，有助于保持对代码的信心。正如创作者所说，其背后的动机是

> *"(...)利用强类型 GraphQL 模式，目的是使您的后端类型安全，同时减少通过代码生成编写样板文件的需要”。*

在类型脚本&流中，`graphqlgen`根据您的 GraphQL 模式生成&支架类型安全解析器。

回购:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [普里斯马实验室](https://github.com/prisma-labs) / [格拉福根](https://github.com/prisma-labs/graphqlgen)

### ⚙️根据您的 GraphQL 模式生成类型安全解析器

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/5622214824dac931e87de711fcf828dd.png)](https://camo.githubusercontent.com/91ad6f1a111d084b5c418b7da20d717017e3d519/68747470733a2f2f696d6775722e636f6d2f633659347447772e706e67)

# graphqlgen

[![CircleCI](img/5c1488dcb5af559d2db5c8a7a627c662.png) ](https://circleci.com/gh/prisma/graphqlgen) [ ![npm version](img/376ed04e35b1c80811bcfded7a71f616.png)](https://badge.fury.io/js/graphqlgen)

基于 TypeScript、Flow & Reason 中的 GraphQL 模式生成& scaffold 类型安全解析器

## 折旧票据

`graphqlgen`已经被官方弃用，取而代之的是[公会](http://the-guild.dev)的项目 [GraphQL 代码生成器](https://graphql-code-generator.com/)。在[这篇](https://www.prisma.io/blog/the-guild-takes-over-oss-libraries-vvluy2i4uevs)博文中了解更多关于 Prisma 和公会的合作。

* * *

*   [关于](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#about)
    *   [亮点](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#highlights)
    *   [动机](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#motivation)
    *   [支持的语言](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#supported-languages)
*   [入门](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#getting-started)
    *   [尝试一个项目初始化器](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#try-out-a-project-initializer)
    *   [添加到现有项目](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#add-to-existing-project)
    *   [文档](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#documentation)
*   [附录](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#addendum)
    *   [社区](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#community)
    *   [项目状态](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#project-status)
    *   [现有技术](https://raw.githubusercontent.com/prisma-labs/graphqlgen/master/#prior-art)

## 关于

### 突出

*   **模式-首先在 SDL 进行**设计，以衍生出理想的类型
*   **类型安全**解析器，具有精确签名，包括`parent`、`args`和返回类型
*   精确解析器类型让你的编辑器智能感知工作
*   **生态系统互操作** codegen 适用于 Yoga 1 或 Apollo 服务器，支持[更漂亮](https://github.com/prettier/prettier)和 [graphql-import](https://github.com/prisma/graphql-import) 开箱即用

### 动机

在类型安全环境中编程有助于增强对代码完整性的信心。旨在利用 GraphQL 类型系统使您的解析器完全…

</article>

[View on GitHub](https://github.com/prisma-labs/graphqlgen)

### 3。GraphQL 代码生成器

[GraphQL 代码生成器](https://graphql-code-generator.com/)是一个从你的 GraphQL 模式中生成代码的工具，支持定制插件&模板，所以无论你使用什么语言(*即 TypeScript、React、Angular* ) GraphQL 代码生成器都将是一个巨大的帮助。

回购:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[dotansimha](https://github.com/dotansimha)/[graph QL-code-generator](https://github.com/dotansimha/graphql-code-generator)

### 基于 GraphQL 模式和 GraphQL 操作(查询/变异/订阅)生成代码的工具，灵活支持定制插件。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![CodeGen](img/5faed2d2cff5809d8642caad2cddefa5.png)](https://graphql-code-generator.com)

[![npm version](img/3de75ef20c4b02f53f1bf0be2b6c13f0.png)](https://badge.fury.io/js/%40graphql-codegen%2Fcli)[![CircleCI](img/4c92b7e56a2f4e4bfbca2053ca583e72.png)](https://circleci.com/gh/dotansimha/graphql-code-generator/tree/master)[![Discord Chat](img/4d4c7bece0f349e17ee1084df3a9094d.png)](https://discord.gg/xud7bH9)[![code style: prettier](img/d9e4d54c4df36b5be49b1a1209acdffa.png)](https://github.com/prettier/prettier)[![renovate-app badge](img/fb9565e3f2642c35fa315049c444dadc.png)](https://renovateapp.com/)

[graphql-code-generator.com](https://graphql-code-generator.com)

[GraphQL 编码 1.0 在这里！](https://graphql-code-generator.com/docs/migration/from-0-18)

GraphQL 代码生成器是一个从 GraphQL 模式中生成代码的工具。无论您是开发前端还是后端，您都可以利用 GraphQL 代码生成器从您的 GraphQL 模式和 GraphQL 文档(查询/变异/订阅/片段)生成输出。

通过分析模式和文档并对其进行解析，GraphQL Code Generator 可以基于预定义的模板或基于自定义的用户定义的模板输出各种格式的代码。无论您使用哪种语言，GraphQL 代码生成器都能满足您的需求。

GraphQL 代码生成器让你选择你需要的输出，基于*插件*，非常灵活和可定制。您也可以编写您的*插件*来生成符合您需求的定制输出。

您可以在您的浏览器上试用这个工具，并查看一些有用的示例。查看 [GraphQL 代码生成器实例](https://graphql-code-generator.com/#live-demo)。

我们目前支持和…

</article>

[View on GitHub](https://github.com/dotansimha/graphql-code-generator)

###### 封面图片由 [unDraw](https://undraw.co/) 提供