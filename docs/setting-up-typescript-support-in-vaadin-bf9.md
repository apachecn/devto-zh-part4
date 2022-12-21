# 在 Vaadin 中设置 TypeScript 支持

> 原文：<https://dev.to/marcushellberg/setting-up-typescript-support-in-vaadin-bf9>

**更新**。Vaadin 14.5 和更高版本带有内置的类型脚本支持。以下步骤仅适用于 14.4 及更低版本。

不久前，我创建了一个小型演示项目来展示从 Vaadin 14 开始，您现在可以轻松地配置您的项目来支持前端模板中的 TypeScript。

[Giovanni](https://github.com/heruan) ，一位活跃的社区成员，[向我指出](https://github.com/marcushellberg/vaadin-lit-typescript/issues/1)我没有真正解释让打字稿工作的步骤。与其把这些步骤埋在 GitHub 标签里，我想我应该写一篇简短的博客文章来概述这些步骤。希望这能在以后帮助其他人。

本指南经过 Vaadin 14 测试。

除了 Java，您还需要安装[节点](https://nodejs.org)。

## 准备好您的 Vaadin 项目

如果你没有 Vaadin 14+项目，在[vaadin.com/start](https://vaadin.com/start)下载一个入门软件。

如果您有一个运行早期版本 Vaadin 的 Vaadin 项目，请阅读[迁移指南](https://vaadin.com/docs/v14/flow/v14-migration/v14-migration-guide.html)并在继续之前升级项目。

运行一次`mvn package`以确保 Vaadin 获取所有需要的依赖项

## 安装 TypeScript 及其 Webpack 加载器

在项目文件夹中，运行

`npm install --save-dev typescript ts-loader`

## 配置 Webpack 处理 TypeScript

打开`webpack.config.js`并将`module.export`声明编辑为以下内容:

```
module.exports = merge(flowDefaults, {
  resolve: {
    extensions: ['.ts', '.tsx', '.js', '.json']
  },
  devtool: 'inline-source-map',
  module: {
    rules: [{
      // Include ts, tsx, js, and jsx files.
      test: /\.tsx?$/,
      exclude: /node_modules/,
      loader: 'ts-loader',
    }],
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们将 Webpack 配置为通过 typescript 编译器运行所有的 TypeScript 和 JavaScript 文件。我们还将 JavaScript 文件传递给 TypeScript 编译器的原因是，如果需要，它可以转换新的 JavaScript 语法来支持旧的浏览器。

## 配置 TypeScript 编译器

在项目根目录中，添加一个新文件`tsconfig.json`，其内容为

```
{  "compilerOptions":  {  "target":  "esnext"  /*  Specify  ECMAScript  target  version:  'ES3'  (default),  'ES5',  'ES2015',  'ES2016',  'ES2017','ES2018'  or  'ESNEXT'.  */,  "module":  "es2015"  /*  Specify  module  code  generation:  'none',  'commonjs',  'amd',  'system',  'umd',  'es2015',  or  'ESNext'.  */,  "declaration":  true  /*  Generates  corresponding  '.d.ts'  file.  */,  "sourceMap":  true  /*  Generates  corresponding  '.map'  file.  */,  "strict":  true  /*  Enable  all  strict  type-checking  options.  */,  "moduleResolution":  "node"  /*  Specify  module  resolution  strategy:  'node'  (Node.js)  or  'classic'  (TypeScript  pre-1.6).  */,  "allowSyntheticDefaultImports":  true  /*  Allow  default  imports  from  modules  with  no  default  export.  This  does  not  affect  code  emit,  just  typechecking.  */,  "esModuleInterop":  true  /*  Enables  emit  interoperability  between  CommonJS  and  ES  Modules  via  creation  of  namespace  objects  for  all  imports.  Implies  'allowSyntheticDefaultImports'.  */,  "experimentalDecorators":  true  /*  Enables  experimental  support  for  ES7  decorators.  */  }  } 
```

Enter fullscreen mode Exit fullscreen mode

查看演示中的[文件，了解更多您可以配置的选项。](https://github.com/marcushellberg/vaadin-lit-typescript/blob/master/tsconfig.json)

## 从 Java 加载一个 TypeScript 文件

配置完成后，您现在可以将 TypeScript ( `.ts`)文件放在`/frontend`文件夹中，并使用来自 Java 的`@JsModule`加载它们。

*/src/main/java/.../LitTemplate.java*

```
@Tag("lit-template")
@JsModule("./src/lit-template.ts")
public class LitTemplate extends Component {
//...
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。

你可以在这里查看完整的演示源:[https://github.com/marcushellberg/vaadin-lit-typescript/](https://github.com/marcushellberg/vaadin-lit-typescript/)

如果你有任何问题，请在下面发表或在 Twitter 上联系我