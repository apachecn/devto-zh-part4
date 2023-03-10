# 如何解决？带有 typescript 和 webpack 的 graphql 文件

> 原文：<https://dev.to/open-graphql/how-to-resolve-import-for-the-graphql-file-with-typescript-and-webpack-35lf>

大家好，我是莫森·扎雷扎代尼，我是适马电信有限责任公司的软件工程师。

在适马，为了开发我们的后台应用程序，我们决定使用 GraphQL、Typescript 和 Apollo server。我们选择模式优先的方法，并将我们的模式存储在单独的`.graphql`文件中。为了在我们的 Typescript 代码中导入我们的模式，我们最终使用 Webpack 作为我们的 transpiler。

因为我正在寻找一种方法来做到这一点，我没有找到任何关于这个问题的好教程，所以我为未来的研究人员提供这个教程！🤠

首先，您需要将以下配置添加到您的`tsconfig.json`中。这将告诉 Typescript 使用除了`node_modules/@types`之外的`src/@types`文件夹来识别你的类型。