# 我希望得到关于我打字技巧和 NPM 模块的反馈。

> 原文：<https://dev.to/dechamp/i-would-love-feedback-on-my-typescript-skills-and-npm-module-5goc>

我写了一篇关于我的第一个公共模块插件的文章，但是从那以后我用 TypeScript 重写了大部分内容。它肯定会使用一些输入，因为我只是在 TypeScript 的第 3 周。

[![dechamp](img/6e88502e1d2cdc4ad1bb73699652d17b.png)](/dechamp) [## 第一个 NPM 包，带有 jsonSchema 验证的快速自动路由

### DeChamp Jun 11 ' 192min read

#opensource #discuss #showdev #express](/dechamp/first-npm-package-express-autoroute-with-jsonschema-4cma)

我建立的 NPM 模块是用于快速路由的。它将允许您通过一个配置文件创建您的路由，以及跨所有路由或每条路由设置您的中间件。

[NPM 模块-@ dechamp/express-自动路线](https://www.npmjs.com/package/@dechamp/express-auto-route)

它为请求和响应打包了两个可选的中间件。

中间件将启用配置中的选项，允许您指向 [Json 模式](https://json-schema.org/)，这将验证您的输入以确保它是合格的。

它可以被配置为处理查询、主体和参数(express path :yourVar)。

我计划在整个代码中添加更多的 TypeScript 类型，最终去除`"noImplicitAny": false`。

请随时将它导入到您的项目中，使用它，并让我知道它是否容易实现。README.md 文件怎么样？这一切有意义吗？

我知道 express 已经非常容易使用，这个软件包对一些人来说没有任何价值，这没关系。这不是我想听到的。我在寻求提高打字技能的建议，如何改进软件包，以及它是否能让你的生活更轻松。

[NPM 模块-@ dechamp/express-自动路线](https://www.npmjs.com/package/@dechamp/express-auto-route)

非常感谢！