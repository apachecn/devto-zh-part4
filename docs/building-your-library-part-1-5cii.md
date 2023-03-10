# 构建您的图书馆:第 1 部分

> 原文：<https://dev.to/4nduril/building-your-library-part-1-5cii>

## 前言

本文是“发布现代 JavaScript(或 TypeScript)库”系列的第 4 部分。查看简介中的动机和其他部分[的链接。](https://dev.to/4nduril/publish-a-modern-javascript-or-typescript-library-3hc3)

*注意:*我已经在本系列的[第 3 部分](https://dev.to/4nduril/compiling-modern-language-features-with-the-typescript-compiler-36m3)中承诺，下一篇文章将是关于导出类型的。但是请原谅我。首先，我们将利用现有资源。接下来是类型。

## 我们的第一次建造

到目前为止，我们已经分别讨论了如何设置 Babel 或 TypeScript 编译器，以传输我们精心制作的库代码。但是我们实际上并没有使用它们。毕竟，我们在这里工作的目标应该是一个功能完整的构建链，完成发布我们的库所需的一切。

所以我们现在就开始吧。正如您可以从这篇文章的标题中看出的，我们将使用我们安装和配置的工具箱中的每一项来改进我们的构建。虽然“普通”的帖子都只关注一个工具的一个用途，但是这些“构建”文章将收集我们拥有的各种工具组合的所有配置。

我们将利用 NPM 脚本来启动我们所做的一切。对于 JavaScript/TypeScript 项目来说，这是很自然的事情:你总是`npm install`、`npm test`和`npm start`，所以我们也会`npm run build`。

因为今天我们会比较快地完成它。我们只能在 Babel 和 TSC 之间进行选择，而 transpiling 是我们在构建时唯一要做的事情。

## 用巴别构建 JavaScript

您可以在项目根目录下的`package.json`文件中定义一个`build`脚本。相关的键是`scripts`和`module`，我们对其进行了修改，使其至少包含以下内容:

```
{
  // ...
  "module": "dist/index.js",
  "scripts": {
    "build": "babel -d dist/ src/"
  }
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

### 使用`module`

指向一个包的入口文件的标准键是`main`。但是我们这里用的是`module`。这可以追溯到联邦议院提出的[提案。这里的想法是，`main`键下的入口点只对 ES5 有效。尤其是关于模块语法。那里的代码应该使用像 CommonJS，AMD 或 UMD，但不是 ESModules。虽然像 Webpack 和 Rollup 这样的捆绑器可以处理遗留模块，但它们不能彻底改变它们。(如果你忘了为什么，再读一遍关于巴别塔的文章。)](https://github.com/rollup/rollup/wiki/pkg.module)

因此，该提案指出，您可以在`module`下提供一个入口点，以表明那里的代码正在使用现代的 ESModules。如果你的 package.json 中有一个`module`键，打包程序会首先查看，在这种情况下就使用它。只有当他们找不到的时候，他们才会退回到`main`。

### 谓通天塔

名为`build`的“脚本”只是对 Babel 命令行界面(CLI)的一次调用，其中有一个选项`-d dist`，它告诉 Babel 将传输的文件放在哪里(`-d` : `--out-dir`)。最后，我们告诉它在哪里可以找到源文件。当我们给它一个像`src`一样的目录时，巴别塔将传输它理解的每一个文件。即，具有以下列表中的扩展名的每个文件:`.es6,.js,.es,.jsx,.mjs`。

## 用巴别塔构建打字稿

这个和上面差不多。唯一的区别是我们传递给 Babel CLI 的选项。`package.json`中的相关部分看起来是这样的:

```
{
  // ...
  "module": "dist/index.js",
  "scripts": {
    "build": "babel -d dist/ --extensions .ts,.tsx src/"
  }
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我上面提到的，Babel 不知道它应该在`src`中传输`.ts`和`.tsx`文件。我们必须用`--extensions`选项明确地告诉它。

## 用 TSC 构建类型脚本

为了使用 TypeScript 编译器，我们在`package.json`中这样配置我们的构建:

```
{
  // ...
  "module": "dist/index.js",
  "scripts": {
    "build": "tsc"
  }
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们不需要告诉 TSC 在哪里找到文件，将文件放在哪里，因为这些都在 tsconfig.json 中。我们的构建脚本唯一要做的就是调用`tsc`。

## 准备运行

就是这样。你现在所要做的就是输入
来获得生产就绪的代码

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

在`dist`目录中有你的编译库代码。这可能看起来不多，但是我告诉你，如果你把它打包或者安装在[除了注册表之外的其他方式](https://docs.npmjs.com/cli/install.html)，它就可以在应用程序中使用。也没那么糟糕。它可能没有导出类型，没有测试，没有贡献助手，没有语义版本控制，没有构建自动化，**但是**它提供了树可动摇的现代代码——这比其他许多公司都多。

请务必查看我为本系列设置的[示例代码库](https://github.com/4nduril/library-starter)。目前有三个分支:`master`、`typescript`和`typescript-tsc`。Master 反映了我个人对 JS 项目工具的选择，`typescript`是我在 ts 项目中的选择，第三个是第二个的替代。自述文件中有一个包含分支及其功能的表格。

接下来:类型检查和提供类型声明(这次是真的；) )

* * *

一如既往，非常感谢我的朋友 Tim Kraut 校对了这篇文章！