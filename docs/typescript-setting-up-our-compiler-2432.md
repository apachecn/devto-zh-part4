# TypeScript:设置我们的编译器

> 原文：<https://dev.to/miku86/typescript-setting-up-our-compiler-2432>

## [T1】简介](#intro)

在上一篇文章中，我们谈到了用 TypeScript 编写时的[基本工作流程。](https://dev.to/miku86/typescript-basic-workflow-pe)

在今天的文章中，我们将学习如何配置 TypeScript 编译器。

* * *

## 概述

我们的配置可以在名为`tsconfig.json`的文件中进行，也可以在命令行中进行。

对于本文，我们使用`tsconfig.json`。

如果我们没有一个`tsconfig.json`，就要用`tsc [filename]`来编译。

如果我们有一个`tsconfig.json`，我们必须使用`tsc`来编译，`tsc [filename]`会忽略`tsconfig.json`

* * *

## 创造一个`tsconfig.json`

我们可以用`tsc --init`得到一个默认的`tsconfig.json`。

这太棒了，因为创建的`tsconfig.json`有很多有用的`compilerOptions`在里面，包括评论。

```
{
  "compilerOptions": {
    /* Basic Options */
    // "incremental": true,                   /* Enable incremental compilation */
    "target": "es5",                          /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'. */
    "module": "commonjs",                     /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */
    // "lib": [],                             /* Specify library files to be included in the compilation. */
    // "allowJs": true,                       /* Allow javascript files to be compiled. */
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在`compilerOptions`下面，你[还可以修改](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)`files``include``exclude``compileOnSave`和`extends`。

大部分时间我用:

```
"exclude":[
  "node_modules",
  "build"
] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 基本选项

有将近 100 个选项，我们来说一些基本的:

*   `target`:指定 ECMAScript 目标版本:`es3`从 1999 年是默认选项，我使用`es2016`，因为它有所有的新东西，如`const`和`async/await`，但不是太新，不能打破东西。
*   `declaration`:生成一个`.d.ts`，用于发布一个包
*   `sourceMap`:生成一个源码图，对调试有用，我用`true`。
*   `outDir`:编译文件的输出目录，我用的是`"./build"`
*   `removeComments`:删除注释，我用`true`
*   `strict` : **非常有用** -启用所有严格的类型检查选项，这些是下面的选项。我用`true`
*   `noUnusedParameters`:报告未使用的参数，我使用`true`
*   `noImplicitReturns`:报告缺失退货，我用`true`
*   `allowSyntheticDefaultImports`:允许从没有默认导出的模块默认导入，我用的是`true`
*   `esModuleInterop`:为所有导入创建名称空间对象，我使用`true`

* * *

## 下一部分

我们将学习如何编写一些基本代码。

* * *

## 进一步阅读

[`tsconfig.json`Docs](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)
[TypeScript 编译器选项](https://www.typescriptlang.org/docs/handbook/compiler-options.html)
[我的个人 tsconfig.json](https://gist.github.com/miku86/9f47c3ebf34cebb2bf47c97d46ffd3d6)