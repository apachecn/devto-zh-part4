# 使用 node.js 和 npm 创建 cli 工具

> 原文：<https://dev.to/pgarciacamou/creating-a-cli-tool-using-nodejs-and-npm-315>

**2019 年 8 月 5 日**

**TLDR 在博文的最后。**

## 简介

如果你和我一样，想少读书，多做事。我会尽量简短。

我们将构建一个简单的 CLI 工具，它可以使用`npm`进行全局安装，或者使用`npx`在没有预先安装的情况下执行。这个工具将简单地记录“Hello World！”我将把它命名为`cli-tool`。

换句话说，目标是能够运行`npx cli-tool`或`npm i -g cli-tool && cli-tool`。

## MVP(最小可行产品)

给我看看代码！

最简单的 CLI 工具包括 3 个步骤:

1.  `package.json`(必须包括“名称”和“bin”)
2.  `index.js`(必须包含节点*社邦*)
3.  `npm link`

```
{  "name":  "cli-tool",  "bin":  "./path/to/bin.js"  } 
```

Enter fullscreen mode Exit fullscreen mode

```
#!/usr/bin/env node console.log("Hello World!") 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以在 repo 中运行`npm link`，并享受在终端中运行`cli-tool`的乐趣。注意，如果你想恢复这个，你可能需要运行`npm unlink`。

它是如何工作的？`npm link`从`package.json`获取名称并创建一个到全局模块的符号链接。这里可以阅读更多[。](https://docs.npmjs.com/cli/link)

## MVP + npm + npx

我们也可以使用`npm publish`发布我们的模块。我们必须给`package.json`添加“版本”和“字段”属性。

```
{  "name":  "cli-tool",  "version":  "1.0.0",  "bin":  "./path/to/bin/bin.js",  "fields":  ["./path/to/bin"]  } 
```

Enter fullscreen mode Exit fullscreen mode

注意:我修改了 bin 路径以使其更容易理解。

发布时，该版本将显示在 npmjs.com 中，字段属性是要包括的白名单路径列表。也就是说，只有那些字段会被上传。你可以在这里阅读更多关于“字段”属性[的信息。](https://medium.com/@jdxcode/for-the-love-of-god-dont-use-npmignore-f93c08909d8d)

现在，在 npmjs.com 发布我们的模块后，运行`npx cli-tool`或`npm i -g cli-tool && cli-tool`是可能的。

## 结论/接下来呢？

**MVP 很重要！它向我们展示了我们的目标。不管做什么，都要以一个`package.json`和一个二进制(节点脚本)结束。**

现在我们知道了目标，我们可以使用像 [Webpack](https://webpack.js.org/) 、 [Rollup](https://rollupjs.org/guide/en/) 或 [Brunch](https://brunch.io/) 这样的捆绑器来使用最新的 ECMAScript 特性并将应用程序捆绑到我们的`./path/to/bin`目录中。

我们还可以使用一些工具来帮助我们定义 CLI 工具的行为，比如 [Commander](https://github.com/tj/commander.js#readme) 或 [Yargs](https://yargs.js.org/) 。

最后，我们可以使用一些工具来发布模块，而不用担心跟踪版本、变更、标签等等，比如 [release-it](https://www.npmjs.com/package/release-it) 和 [release](https://www.npmjs.com/package/release) 。

我本人不喜欢重建车轮。我喜欢 [create-react-app](https://facebook.github.io/create-react-app/) ，有一个工具在内部使用`Rollup`和`create-react-app`来创建 react 库，这个工具叫做 [create-react-library](https://github.com/transitive-bullshit/create-react-library) 。我只是删除了不需要的依赖项(即 React ),并确保 package.json 包含模块作为二进制文件运行所需的内容。

但所有这些都不是这篇博文的内容。我将在第 2 部分添加这些工具。

## TLDR

1.  `npm init`
2.  将“名称”、“bin”、“版本”和“字段”添加到`package.json`
3.  将节点“shebang”添加到 JavaScript 脚本
4.  `npm publish`
5.  `npx cli-tool`或`npm i -g cli-tool && cli-tool`

最终结果:

`package.json`

```
{  "name":  "cli-tool",  "version":  "1.0.0",  "bin":  "./path/to/bin/bin.js",  "fields":  ["./path/to/bin"]  } 
```

Enter fullscreen mode Exit fullscreen mode

`./path/to/bin/bin.js`

```
#!/usr/bin/env node console.log("Hello World!") 
```

Enter fullscreen mode Exit fullscreen mode