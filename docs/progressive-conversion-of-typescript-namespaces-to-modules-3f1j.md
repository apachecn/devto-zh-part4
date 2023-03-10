# TypeScript 命名空间到模块的渐进式转换

> 原文：<https://dev.to/ynab/progressive-conversion-of-typescript-namespaces-to-modules-3f1j>

我们喜欢在 YNAB 的打字稿。我们的主要模块之一是我们称之为“共享库”的东西，它是一个相当大的 TypeScript 项目。实际上，它由 3 个库项目和 3 个测试项目组成。它很大。而且，在 TypeScript 支持 es 模块之前，它最初是使用 TypeScript 命名空间编写的。

我们希望开始将这个库转换成使用 ES 模块，以获得各种好处，包括树摇动的能力和更好的开发工具。但是，很明显，我们需要找到一种渐进的方式来做到这一点，因为一些全有或全无的尝试被证明是令人生畏的。成千上万的错误，没有简单或明显的方法来自动转换。

将项目从名称空间逐步转换为模块的指南很少。有[这个 GitHub 问题](https://github.com/Microsoft/TypeScript/issues/12473)有些人在讨论方法，但是在方法上仍然有一些差距。

为了设置渐进式迁移，我们最终做了下面这些对我们很有效的事情。

1.  将`"exclude": ["**/*.m.ts"]`添加到原始 global / namespace 项目中的 tsconfig.json 文件中。这允许您创建扩展名为. m.ts 的模块，并保持`outFile`配置。
2.  在主 tsconfig.json 文件旁边创建一个`tsconfig.module.json`文件。
3.  在 tsconfig.module.json 文件中使用一个指向名称空间项目`"references": [{ "path": "./tsconfig.json" }]`的[项目引用](https://www.typescriptlang.org/docs/handbook/project-references.html)。这使得全局/名称空间*类型*在模块项目中可用。它不发出该项目的代码，但它告诉 TypeScript 假定这些类型在运行时可用。

看起来是这样的:

#### tsconfig.json

```
{​  "compilerOptions":  {​  "outFile":  "../dist/package.js",​  "composite":  true​  },​  "include":  ["**/*.ts"],​  "exclude":  ["**/*.m.ts"]​  } 
```

#### tsconfig.module.json

```
{​  "compilerOptions":  {​  "module":  "esnext"​  }​  "files":  ["./MyClass.m.ts"],​  "references":  [{  "path":  "./tsconfig.json"  }]​  } 
```

通过上面的设置，您可以创建扩展名为`.m.ts`的模块文件，与现有的名称空间文件相邻，如下所示:

```
- MyClass.ts​
- MyClass.m.ts 
```

然后，正如 [@danielrosenwasser](https://dev.to/danielrosenwasser) [指出的](https://github.com/Microsoft/TypeScript/issues/12473#issuecomment-263374060)，你可以使用命名空间代码中的现有代码，并将其包装在模块文件中。你也可以直接导出:

```
export import MyClass = my_namespace.MyClass; 
```

由于这个设置使用的是 ES 模块，所以您现在可以使用 webpack 这样的捆绑器来为 web 消费做准备。在下面的例子中，webpackwill 将与 ts-loader 一起使用。注意 ts-loader 被配置为使用`tsconfig.module.json`配置文件。

#### webpack.config.js

```
module.exports = {​
  entry: './src/MyClass.m.ts',​
  module: {​
    rules: [​
      {​
        test: /\.tsx?$/,​
        use: [​
          {​
            loader: 'ts-loader',​
            options: {​
              configFile: "./tsconfig.module.json" // Use the module project config!​
            }​
          }​
        ]​
      }​
    ]​
  },​
  output: {​
    filename: 'bundle.js',​
    path: path.resolve(__dirname, '../dist')​
  }​
}; 
```

最后，在您的入口网页中，您可以包含对原始名称空间 outFile 和 webpack 包的引用。

#### index.html

```
<head>​
  <script src="dist/package.js"></script> <!-- outFile output from tsc -->​
  <script src="dist/bundle.js"></script> <!-- webpack bundle -->​
</head> 
```

一旦将所有命名空间源文件转换为模块，就可以移除原始命名空间项目并停止加载它。

更详尽的例子可以在这个资源库中找到:[https://github . com/bradymholt/ts-progressive-convert-namespace-modules](https://github.com/bradymholt/ts-progressive-convert-namespace-modules)。