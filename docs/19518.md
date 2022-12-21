# 作为构建工具的 NPM 脚本

> 原文：<https://dev.to/nervewax/npm-scripts-as-a-build-tool-n4>

构建工具通过自动化任务来帮助开发人员节省时间。在这篇文章中，我将解释我如何使用 npm 脚本作为构建工具，通过直接运行包，而不是通过 Grunt、Gulp、Webpack、packages 等等。

对于那些已经熟悉上述工具的人来说，我希望这提供了一个更简单的选择。对于那些以前没有使用过构建工具的人来说，这可能是一个简单的切入点，因为即使只有基本的理解也可以完成很多工作。

## 什么是 npm？

节点包管理器是一个软件注册表，许多开发者通常通过 [npm 命令行界面](https://docs.npmjs.com/cli/npm)下载他们喜欢的包和工具。

要使用 npm，[在您的机器上安装 Node.js](https://nodejs.org/) ，然后您可以通过键入`npm`在您的 shell 中访问 npm cli。

另请参见[“下载并安装 Node.js 和 NPM”](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)。

## 脚本示例

一旦你在你的机器上有了 npm，添加一个`package.json`文件到你的项目根目录或者运行`npm init`。这个文件包含了关于你的项目、你添加的包和定义的脚本的信息。一个基本的 package.json 应该是这样的:

```
{  "name":  "test-project",  "version":  "1.0.0",  "scripts":  {  "test":  "echo \"Test works!\""  }  } 
```

“脚本”下是我们所有 npm 脚本的定义位置。用这个 package.json 在目录里面运行`npm test`会在 shell 里面返回`Test works!`，自己试试吧！

## npm 作为构建工具

使用 npm 脚本，我们可以将更复杂的任务放在一起，类似于您可能在其他构建工具中找到的任务。然后，在一些实用程序包的帮助下，我们可以让任务按顺序或并行运行，并在特定文件发生更改时触发。

在 GitHub 上，我用自己的 [package.json](https://github.com/nervewax/npm-compiler/blob/master/package.json) 创建了一个 repo，其中包含了构成基本构建过程的脚本。

在这个文件中，我添加了像`build`、`clean`和`watch`这样的任务，它们运行通过关键字分组的其他子任务。例如`watch:*`指的是从`watch:`开始的所有任务。使用这些任务，该工具可以完成以下任务:

*   将 sass 文件编译成 CSS 文件。
*   缩小 CSS 和 JS 文件。
*   压缩图像和 SVG 文件，为现场直播做好准备。
*   保存更改并刷新浏览器后运行上述任务。

**[github.com/nervewax/npm 编译器](https://github.com/nervewax/npm-compiler)**

如果您想自己开始使用 npm 脚本，请尝试下载 repo 并运行其中的一些任务。一旦您更加熟悉这些，您就可以组装自己的构建过程来满足您的需求。

下面我列出了在我的回购中找到的包以及它们的作用。每个软件包都有自己的说明，可以在相应链接的 npm 页面的自述文件中找到。

### 实用程序

创建一个目录和任何子目录，这在你需要创建文件夹结构时很有用。

onchange -监视文件，然后在添加、更改或删除任何内容时运行任务。

[npm-run-all](https://www.npmjs.com/package/npm-run-all) -让您依次或并行运行多个任务。

[rimraf](https://www.npmjs.com/package/rimraf) -递归运行删除命令，包括只读文件。

当检测到变化时刷新你的浏览器。

### CSS

[node-sass](https://www.npmjs.com/package/node-sass) -将 scss 文件编译成 css。

[postcss-cli](https://www.npmjs.com/package/postcss-cli) -运行上述任务后， [PostCSS](https://github.com/postcss/postcss) 可用于运行更多任务，如下所示:

[autoprefixer](https://www.npmjs.com/package/autoprefixer) -自动添加厂商前缀到任何需要它们的样式中，以便在不同的浏览器中工作。例如`-webkit-`或`-ms-`前缀。

[像素](https://www.npmjs.com/package/pixrem) -为`rem`单位提供`px`的后援。

[cssnano](https://www.npmjs.com/package/cssnano) -压缩 css 文件。

### JS

压缩 js 文件。

[累计](https://www.npmjs.com/package/rollup) -实际上不包括在我的回购中！但是它是处理 js 的替代方法，也处理 es6 模块，所以我自己肯定会考虑使用它。

### 其他东西

html-minifier -压缩 html 文件。

[imagemin-cli](https://www.npmjs.com/package/imagemin-cli) -减小图像文件大小。虽然这可能很费时间，所以这是一个候选的删除。

[svgo](https://www.npmjs.com/package/svgo) -优化并减小 SVG 的文件大小。

## 这就是所有的乡亲

希望，如果你已经阅读了这篇文章，你会对 npm 脚本能做什么有更多的了解。也许这也说服了你放弃一个更复杂的构建工具？

不管怎样，感谢你的阅读，如果你有任何问题，我很乐意帮忙。👋

### 进一步阅读

什么是构建工具？
[css-tricks.com/why-npm-scripts](https://css-tricks.com/why-npm-scripts/)
[gablaxian.com/blog/using-npm-as-a-build-too](https://gablaxian.com/blog/using-npm-as-a-build-tool)
[keithcirkel.co.uk/how-to-use-npm-as-a-build-tool](https://www.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/)