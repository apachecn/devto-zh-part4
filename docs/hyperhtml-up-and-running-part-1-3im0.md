# HyperHTML 启动和运行第 1 部分

> 原文：<https://dev.to/paritho/hyperhtml-up-and-running-part-1-3im0>

如果您还没有听说过 hyperHTML，那么它值得您花时间去研究一下。作为大型流行框架的替代方案，hyper 是一个速度极快、占用空间小的库，可以让您掌控全局。HyperHTML 以极快的 DOM 更新为中心，如果您愿意，可以集成到任何其他框架中(专业提示:您不会想这么做)。

但是眼见为实，让我们启动一个新项目，看看开始使用 hyperHTML 有多简单。本教程将重点介绍使用 hyperHTML 建立开发环境的各种方法。您将了解一些关于 hyperHTML、webpack、rollup 和模块的知识。我们假设你有 web 技术的工作知识，比如 HTML，CSS，当然还有 JavaScript。

让我们从设置本地文件夹结构开始。

```
hyperhtml
|_dist
|_src 
```

> 在本教程中，我们将使用 npm 的一些工具，所以请确保您已经安装了 nodejs。

在根文件夹中，创建一个标准的样板文件`index.html`。到目前为止，享受用 hyperHTML 开发应用程序的最简单方法是使用 unpkg CDN。将该行包含在`index.html` :
的`<head>`中

```
<script src="https://unpkg.com/hyperhtml@latest/min.js"></script> 
```

这使我们能够访问 hyperHTML 变量。创建一个`<div id="app">`，然后在结束身体标记之前，使用梅林无法理解的魔法:

```
<script>
    const app = document.querySelector('#app');
    hyperHTML(app)`<h1>Welcome to Hyper</h1>`;   
</script> 
```

启动您最喜欢的开发服务器(目前，我们使用的是 [http-server](https://www.npmjs.com/package/http-server) 。Http-server 是一个简单的零配置服务器)并加载页面。Tada！HyperHTML 所有人。如果您不确定这里发生了什么，请花点时间研究标记的模板文字。你回来时我们会在这里。

让我们向我们的“应用程序”添加几个 DOM 节点:

```
<script>
    const app = document.querySelector('#app'),
          welcome = (name) => `Intrepid traveler, ${name}, welcome!`;    
    hyperHTML(app)`<h1>Welcome to Hyper</h1>
                    <div class="row">
                      <div class="col-sm-6">
                         <p>${welcome("Steve"}</p>
                      </div>
                    </div>`;
</script> 
```

保存，刷新，放下话筒。但是我们能把这个疯狂的 JavaScript 移到另一个文件吗？

当然可以！

在我们项目的 src 文件夹中创建一个`app.js`文件。将所有 JS 魔术移到 app.js 中，然后将该文件包含在`index.html`中。

```
<script src="./src/app.js"></script> 
```

让我们看看它是否有效；在你保存和刷新之前，把史蒂夫踢出去，让国王理查德狮子心来参加聚会。

```
<p>${welcome("King Richard the Lionheart"}</p> 
```

这是非常非常基本的东西。然而，它表明 hyperHTML 除了库和服务器之外不需要任何东西。你可以用这个开发大量的应用程序，但是你为什么要这样做呢？

让我们利用一些其他工具的力量来使我们的开发生活变得更容易。

* * *

## Webpack

我们要看的第一个工具是 webpack。Webpack 是一个模块打包器和加载器。第一步是在我们的项目本地安装带有 npm 的 webpack。在根文件夹中，运行:

```
npm i webpack
npm i webpack-cli 
```

酷毙了。

既然现在用的是 webpack，就不需要用 CDN 去抓取 hyperHTML 了。我们可以进口。首先，用 npm 安装:

```
npm i hyperhtml 
```

并且在`app.js`的顶部，做导入:

```
import hyperHTML from 'hyperhtml/cjs'; 
```

> 注意:“/cjs”是 hyperHTML 必须与 CommonJS 模块加载器(如 Webpack)一起使用的模块。稍后我们将看到 hyper 给我们的另一个模块，'/esm '。

我们现在可以从`index.html`中移除正在拉入超 html 的脚本标签。我们几乎准备好让 webpack 捆绑一切。我们需要对`index.html`再做一个修改:

将`<script src="./src/app.js" />`改为
`<script src="dist/bundle.js" />`。

最后，我们需要告诉 webpack 从哪里开始捆绑我们的文件。虽然我们可以在没有配置的情况下运行 wepack，但是您将创建的大多数项目都需要一个配置，所以我们现在要设置它。创建一个`webpack.config.js`文件，并将其放在项目根目录下。在这个文件中，我们需要一个非常简单的配置:

```
const path = require('path');

module.exports = {
  entry: './src/app.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
}; 
```

现在，你期待已久的时刻。在您的项目根目录命令行上，运行`npx webpack`。如果您收到一些警告或错误，请不要担心。你现在应该可以在你的`dist`文件夹中找到一个`bundle.js`文件。启动`http-server`看看吧！

准备好接受更多的魔法了吗，我年轻的徒弟？只需几个步骤，我们就可以导入 CSS 文件。观看和学习:

*   创建一个`styles.css`文件，放在 src 文件夹中。包括一些样式规则。
*   在`app.js`、`import "styles.css"`
*   在命令行上运行`npm i style-loader css-loader`。
*   将此添加到您的`webpack.config.js`:

```
module: {
     rules: [
       {
         test: /\.css$/,
         use: [
           'style-loader',
           'css-loader'
         ]
       }
     ]
  } 
```

*   再跑一次`npx webpack`看闪电从你手指间飞过！有关更深入的信息，请参见 [webpack 文档。](https://webpack.js.org/guides/asset-management/#loading-css)

随意进出口，直到你的心满意足。通过对 hyper + webpack 的快速介绍，我们看到了在现代 webdev 环境中使用 hyperHTML 是很容易的。

* * *

## Rollup

我们可以使用的另一个伟大的模块捆绑器是`rollup.js`。您可能为您的下一个项目选择 rollup 的一个原因是，它利用了一种称为树摇动的技术。树抖动是一个术语，我们用它来指代捆绑我们实际使用的代码。有时我们可能不需要整个库，只需要其中的一个函数。摇树允许这样做，我们正在摇掉树上的枯叶(死代码)。Webpack 也提供了树抖动，但是在这之前还有另一个配置要设置。Rollup 自动使用树摇动。

如您所料，我们的第一步是在项目中安装 rollup。

```
npm i rollup 
```

您已经熟悉了`webpack.config.js`，所以我们需要的新文件`rollup.config.js`应该不会有什么意外。

```
module.exports = {
  input: 'src/app.js',
  output: {
    file: 'dist/bundle.js',
    format: 'es'
  }
}; 
```

将`rollup.config.js`放在你的根中。

现在，rollup 是一个基本的实用程序，具有许多现成的特性。然而，你可以安装许多插件来进一步增强你的应用程序。这使您能够只使用您需要的东西，而不是更多。在下一部分中会有更多关于插件的内容。

rollup 的一个警告是，您导入的所有内容都必须是相对链接。因此，例如，当我们使用 webpack 时，从节点模块导入 hyperHTML 将无法使用 rollup。我们可以安装一个插件来完成这个任务，[roll up-plugin-node-resolve](https://github.com/rollup/rollup-plugin-node-resolve)，但是因为这是一个简单的项目，我们将只链接到我们需要的文件。

在`app.js`中，将`import hyperHTML from 'hyperhtml/cjs'`更改为
`import hyperHTML from '../../node_modules/hyperhtml/esm'`，前提是您的 node_modules 目录比/src 目录高两级。

现在，删除 styles.css 的导入。

现在，从命令行运行 rollup】

```
rollup -c 
```

如果我们刷新我们的网页，我们看到一切都在工作。

* * *

我希望你喜欢这篇关于使用 hyperHTML 建立和运行环境的介绍性文章。在下一部分，我们将看看一些生活质量插件，它们将使应用程序开发更加容易。所以请继续关注代码向导！