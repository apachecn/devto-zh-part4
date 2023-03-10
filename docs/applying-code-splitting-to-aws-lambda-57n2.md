# 对 AWS Lambda 应用代码拆分

> 原文：<https://dev.to/matttyler/applying-code-splitting-to-aws-lambda-57n2>

无服务器英雄崔琰最近提出了一个问题[“完整的 AWS SDK 到底有多贵”](https://www.freecodecamp.org/news/just-how-expensive-is-the-full-aws-sdk-3713fed4fe70/)？很多人关心冷启动，所以关心如何使我们的功能尽可能精简以确保最小的冷启动和最快的执行是有意义的。毕竟，时间就是金钱——尤其是当我们按 100 毫秒付费的时候。

在 Yan 的文章中，他指出可以通过几种方法减少 node 导入特定 SDK 客户端所花费的时间。最简单的方法是导入所需的特定客户端，而不是整个 SDK。所以与其这样做；

```
import { S3 } from "aws-sdk"; 
```

你会这么做。

```
import S3 from "aws-sdk/clients/s3"; 
```

此外，Yan 展示了使用 bundler(特别是 webpack)可以提高执行时间。通常，这两种优化都会在冷启动时节省大约 100 毫秒。这种节省只发生在冷启动时，因为导入只在第一次调用时被解析——如果您试图进行动态导入，那么这可能会导致每次调用都有损失。对于特别大的依赖项(如 AWS SDK)，捆绑和标准导入在执行上没有明显的区别。无论如何，很容易得出这样的结论:更大的依赖性会导致额外的成本。长期以来，最小化依赖性一直是一个很好的建议，AWS Lambda 并没有改变这一点。

这确实给了我一个想法——代码分割作为一种优化 web 应用程序加载时间的方法，已经在前端世界流行了一段时间。通过代码分割，前端开发人员可以构建仅在有即时需求时才延迟加载依赖项的应用程序。因此，如果用户只需要与 web 应用程序的一小部分进行交互，他们就不必等待加载 web 应用程序的每个部分。在一个无服务器的应用程序中有可能使用代码分割吗？这样做实际吗？让我们来了解一下！

首先，在一个无服务器的应用程序中，代码分割会是什么样子呢？我认为一个最小的例子应该是这样的:

```
const handlers = new Map<string, () => void>();
const modules = new Map<string, string>();

// ...

const handler = (event) => {
    if (!modules.has(event)) {
        throw new Error("No module to available to handle event");
    }

    const module = modules[event];
    if (!handlers.has(module)) {
        handlers[module] = require(module);
    }

    handlers[module](event);
} 
```

现在，我不知道这是否明智。我相信您可以找到这种优化可能有所帮助的用例，但我怀疑它是否会特别普遍。你当然可以证明以这种方式重载处理程序会违反关注点分离，例如，也许你最好编写单独的处理程序函数。

当我开始考虑这个问题时，我正在寻找编写一个无服务器应用程序的方法，该应用程序可以删除在运行集成测试后未能清理的资源。在这种情况下，我需要从 AWS SDK 导入多个不同的客户端来处理不同的资源类型——也就是说，在处理程序执行之前，我不知道需要导入哪些客户端。让我们来看一下这个例子。

想象一个如下所示的项目结构；

```
├── DeleteResources
│   ├── index.ts
│   ├── lib.ts
│   ├── resourcegroups
│   │   └── group.ts
│   ├── s3
│   │   └── bucket.ts
│   └── servicecatalog
│       └── cloudformationproduct.ts 
```

在这种情况下，`index.ts`文件包含处理程序逻辑。它使用 SQS 队列中包含资源类型和资源 ARN 的条目。资源的文件夹结构被特意设计成与资源类型记录相匹配，以便于确定需要导入什么文件——例如，`AWS::S3::Bucket`将映射到`s3/bucket`中的文件。拥有某种安全的事件到模块的映射来防止可能加载不安全代码的事件注入是很重要的。再往下，我还使用了一个 webpack 特性，它恰好限制了可以动态导入的代码；但是无可否认，任何安全改进都是巧合。

```
import { SQSHandler, SQSRecord } from "aws-lambda";
import { Delete } from "./lib";

interface Payload {
    ResourceArn: string
    ResourceType: string
}

async function DeleteRecord (record: SQSRecord): Promise<void> {
    const { ResourceType, ResourceArn } = JSON.parse(record.body) as Payload;
    return await Delete(ResourceType, ResourceArn);
}

export const handler: SQSHandler = async (event) => {
    await Promise.all(event.Records.map(DeleteRecord));
} 
```

`./lib`中的删除函数就是神奇之处。事实证明，您可以利用 webpack 中的代码分割功能来完成繁重的工作。看起来是这样；

```
export const Delete = async (type: string, arn: string): Promise<void> => {
    const [ _, service, resource ] = type.split("::").map(e => e.toLocaleLowerCase());
    const mod = await import(
        /* webpackInclude: /(s3|servicecatalog|resourcegroups)/ */
        /* webpackMode: "lazy-once" */
        `./${service}/${resource}`);

    await mod.default(arn);
} 
```

在本例中，我使用一个带有额外元数据的动态导入语句来告诉 webpack 如何处理动态导入。`webpackInclude`语句告诉 webpack，我将只在运行时尝试加载匹配以下正则表达式的文件。import 语句的参数已经告诉 webpack 它需要在当前目录中寻找可能的候选文件。最后，`webpackMode`指令告诉 webpack，我想在使用模块时对其进行一次惰性加载，并将其缓存起来，以防模块需要再次解析。

特定资源类型的删除处理程序如下所示。注意，它只导入它需要的客户机，因此它不需要加载整个 aws-sdk 包。

```
import catalog from "aws-sdk/clients/servicecatalog";
import { SplitArn, Call } from "../lib";

const re = new RegExp(/^product\/(?<ProductId>.+)/);

export default async function DeleteServiceCatalogCloudFormationProduct(arn: string) {
    const { resource } = SplitArn(arn);
    const [ _, Id ] = re.exec(resource);
    // Here I have wrapped the API call to ignore deletion errors from 
    // resources that do not exist
    await Call(() => new catalog().deleteProduct({ Id }));
} 
```

这确实需要对 webpack 进行一些额外的配置，才能使其正常工作。我一直在使用由 Rich Buggy [生产的默认 webpack 配置，但发现它导致“分割代码”被输出到错误的位置。这意味着它无法正确上传到 S3。我仍然使用 Rich 的插件，但我修改了配置以在多编译器模式下使用 webpack，这解决了问题。这是我最后的网络包配置:](https://github.com/SnappyTutorials/aws-sam-webpack-plugin) 

```
const AwsSamPlugin = require("aws-sam-webpack-plugin");
const awsSamPlugin = new AwsSamPlugin();
const path = require("path");

module.exports = Object.entries(awsSamPlugin.entry()).map(([name, entry]) => {
  return {
    entry,
    output: {
      filename: "app.js",
      libraryTarget: "commonjs2",
      chunkFilename: "[id].js",
      path: path.join(__dirname, `/.aws-sam/build/${name}`)
    },
    devtool: false,
    resolve: {
      extensions: [".ts", ".js"]
    },
    target: "node",
    externals: [
      "aws-sdk", 
      /aws-sdk\/clients\/.*/
    ],
    mode: process.env.NODE_ENV || "production",
    optimization: {
      minimize: false
    },
    module: {
      rules: [
        {
          test: /\.tsx?$/,
          loader: "babel-loader"
        }
      ]
    },
    plugins: [
      awsSamPlugin
    ]
  }
}) 
```

执行生成命令会导致以下结果:

```
└── build
    ├── DeleteResources
    │   ├── 0.js
    │   ├── 1.js
    │   ├── 2.js
    │   └── app.js 
```

`app.js`文件包含入口点代码，每一个具体的资源删除函数都被编译成自己的文件，用一个数字标识。在运行时，如果需要，处理程序将从适当的文件中动态导入代码。如果不需要特定的函数，它将永远不会被解析。

我不指望这会带来巨大的成本节约，但这是一个有趣的实验，看看是否有可能在运行时在 AWS Lambda 中实现代码分割和模块的动态解析。