# GraphQL 想再轻松一次——欢迎 GraphQL 宙斯

> 原文：<https://dev.to/aexol/graphql-queries-easy-again-welcome-graphql-zeus-4db2>

今天我发布了 GraphQL Zeus 库。它为 GraphQL 端点提供了生成的强类型库。因此，您不必担心查询中的问题——您可以使用生成的库中的自动完成功能。

对于 TypeScript 中的泛型来说，这是非常复杂的。

[![](img/943dec3934b9734e6b76c84689d24814.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CvGE-rvZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a4nunqxde9gl650yq39s.gif)

你可以在我的回购上看到一个工作实例。如果您有任何意见或功能需求，请写在这里 GitHub 上。

## 怎么用？

```
$ zeus https://faker.graphqleditor.com/aexol/olympus/graphql ./generated 
```

Enter fullscreen mode Exit fullscreen mode

只需提供 GraphQL 端点或文件，它将在所需的位置生成 lib。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[graph QL-编辑](https://github.com/graphql-editor)/[graph QL-宙斯](https://github.com/graphql-editor/graphql-zeus)

### GraphQL 客户端和带有 GraphQL 自动完成库生成⚡⚡⚡的 GraphQL 代码生成器，用于浏览器、nodejs 和 react native

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/543fac0f547aab3b2e87c1d18d59e305.png)](https://raw.githubusercontent.com/graphql-editor/graphql-zeus/mastimg/zeus.gif)

[![npm](img/5c5fd8eb723258a90f37e0f10d69172f.png)](https://www.npmjs.com/package/graphql-zeus)[![Commitizen friendly](img/60582d8559d7e1c7dc0e9bc223be3197.png)](http://commitizen.github.io/cz-cli/)[![npm downloads](img/57d5b4fcc2461bbfcd69e243b7408152.png)](https://www.npmjs.com/package/graphql-zeus)

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