# 通过构建 UI 框架学习 JavaScript:第 4 部分——创建模块捆绑器

> 原文：<https://dev.to/carlmungazi/learn-javascript-by-building-a-ui-framework-part-4-creating-a-module-bundler-11el>

本文是深入研究 JavaScript 系列的第四篇。你可以通过访问与这个项目相关的 [Github 库](https://github.com/CarlMungazi/aprender-js#what-is-this)来查看以前的文章。

本系列并没有全面涵盖每一个 JavaScript 特性。相反，当功能出现在各种问题的解决方案中时，它们会被包含在内。另外，每篇文章都是基于教程和其他开发者开发的开源库，所以和你一样，我也从每篇文章中学到了新的东西。

* * *

在上一篇文章中，我们为我们的框架添加了创建和呈现 DOM 元素的功能，创建了一个示例应用程序，然后成功地测试了它。今天，我们将介绍在浏览器中运行应用程序的必要步骤。

### 第一步

此时，如果我们安装了一个像 [http-server](https://www.npmjs.com/package/http-server) 这样的服务器，并在包含我们的示例应用程序的文件夹中运行它，这个错误就会出现在浏览器控制台`Uncaught ReferenceError: require is not defined`中。这是因为 [`require`](https://nodejs.org/en/knowledge/getting-started/what-is-require/) 函数只存在于节点环境中。它提供了一种访问存在于单独文件中的代码的方法。在浏览器中复制这种行为最简单(也最痛苦)的方法是使用`<script>`标签。

在 [ES 模块](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)出现之前，开发人员使用(并且仍然在使用)T2 的 CommonJS 或者 T4 的 AMD 格式来解决这个问题。这就是像 Webpack 或 package 这样的构建工具的用武之地。从概念上讲，他们的工作很简单。他们收集运行应用程序所需的所有文件，找出这些文件的依赖关系，然后创建一个可以在网络浏览器中运行的大型 JavaScript 文件。复杂性来自于该过程的 *how* 和各种其他酷技巧，比如热重载(每次保存对文件的更改时创建一个新的包)和树抖动(消除未使用的代码)。

创建捆绑器的第一步是创建一个命令行工具，这样我们就可以使用 [npm 脚本](https://www.tutorialdocs.com/article/npm-scripts-tutorial.html)来启动一切。我们的框架`aprender`已经有了一个`package.json`文件，所以我们从添加以下命令开始。

```
{  "name":  "aprender",  "version":  "1.0.0",  "description":  "",  "main":  "",  "scripts":  {  "test":  "node tests",  "demo":  "maleta demo/index.html --entry index.js"  }  } 
```

此时，值得探究一下当我们在终端中键入`npm run demo`时会发生什么。在运行荐之前，我们首先在`aprender`和我们的构建工具之间创建一个 [`symlink`](https://medium.com/dailyjs/how-to-use-npm-link-7375b6219557) ，我们称之为`maleta`。符号链接由以下各项创建:

*   在与`aprender`相同的文件夹级别上创建名为`maleta`的文件夹
*   在终端中，导航到`maleta`并输入`npm link`
*   导航到`aprender`并输入`npm link maleta`

当`npm run demo`被执行时，npm 抓取一个渲染者的`package.json`文件中的`scripts`对象，并运行分配给属性`demo`的任何命令。演示命令的第一部分引用了我们的模块捆绑器`maleta`。npm 将[处理](https://github.com/npm/cli/blob/v6.9.0/lib/run-script.js#L61) `maleta`的`package.json`文件，并寻找一个名为`bin`的对象。看起来是这样的:

```
"bin": {
  "maleta": "bin/cli.js"
} 
```

任何 npm 包中的`bin`文件夹都包含可执行文件。`maleta`的值是文件`cli.js`的路径，包含以下代码:

```
#!/usr/bin/env node 
const program = require('commander');
const version = require('../package.json').version;
const bundler = require('../src/bundler');

program.version(version);

program
  .command('serve <filename>')
  .description('serves the files')
  .option(
    '--entry <file>',
    'set the name of the entry JS file'
  )
  .action(bundle);

program
  .command('help [command]')
  .description('display help information for a command')
  .action(function(command) {
    let cmd = program.commands.find(c => c.name() === command) || program;
    cmd.help();
  });

const args = process.argv;

// Make serve the default command except for --help
if (args[2] === '--help' || args[2] === '-h') args[2] = 'help';
if (!args[2] || !program.commands.some(c => c.name() === args[2])) args.splice(2, 0, 'serve');

program.parse(process.argv);

function bundle (entryJsFile, command) {
  bundler(entryJsFile, {
    entryJsFile: command.entry
  });
} 
```

这个文件由操作系统的外壳执行。npm 通过使用节点的 [`child_process`](https://nodejs.org/api/child_process.html#child_process_child_process_spawn_command_args_options) 方法来做到这一点。文件顶部的 shebang `#!/usr/bin/env node`告诉您的操作系统在执行文件时使用哪个解释器或应用程序(如果您使用的是 Windows，这将略有不同)。当节点进程启动时，任何指定的参数都被传递给 [`process.argv`](https://nodejs.org/docs/latest/api/process.html#process_process_argv) 属性。前两个参数指的是启动该进程的可执行文件的绝对路径名和正在执行的 JavaScript 文件的路径。从索引 2 开始的每个参数都被正在执行的代码使用。

Maleta 的 CLI 工具是使用 [commander](https://github.com/tj/commander.js/) 构建的。Commander 使用多种方法公开一个对象。我们可以使用`version`方法，通过键入`maleta -V`或`maleta --version`来返回 bundler 版本。之后，我们使用`command`方法开始创建我们的命令。`command`采用以下语法编写的一个参数`command <requiredArg> [optionalArg]`。我们的 CLI 工具有两个命令——一个服务于应用程序，另一个打印帮助文本。当用户运行帮助命令时，显示通过`description`指定的字符串。`action`方法用于指定执行命令时运行的回调函数。它接收通过`<>`或`[]`括号传入的参数和 commander 对象，commander 对象在其属性中具有任何指定选项的名称。

受[包](https://github.com/parcel-bundler/parcel/blob/v1.10.3/src/cli.js#L181)的启发，如果没有参数被传递，我们将`serve`设为默认参数，然后使用 commander 的`parse`方法将参数添加到 commander 对象中。最后，`bundle`用入口文件调用导入的`bundler`函数。

### 工作中的捆绑者

Maleta 的大部分结构借鉴了 [Minipack](https://github.com/ronami/minipack) ，这是一个由 [Ronen Amiel](https://github.com/ronami) 编写的类似项目，解释了捆扎机的工作原理。唯一的区别是 Maleta 捆绑了 es 和 CommonJS 模块，有一个 CLI 工具，并启动一个服务器来运行应用程序。我们 bundler 工作的核心是依赖图。这将列出应用程序中使用的所有文件以及所有依赖项。在构建该图之前，我们将使用条目文件创建一个具有以下结构的`rootAsset`对象:

```
const rootAsset = {
  outDir: '', // the path of the directory where the bundle will created
  content: '', // the code in the file
  entryJsFilePath: '', // the path of the entry JavaScript file
  rootDir: '', // the path of the directory where the entry file lives
  dependencyGraph: '', // the dependencies of the entry file
  ast: '' // an abstract syntax tree created from the code in the file
} 
```

捆绑器应该能够处理 JavaScript 或 HTML 文件作为入口文件，但为了简单起见，Maleta 将只接受 HTML 文件作为起点。创建`rootAsset`对象的函数是:

```
function createRootAssetFromEntryFile(file, config) {
  rootAsset.content = fs.readFileSync(file, 'utf-8');
  rootAsset.rootDir = getRootDir(file);
  rootAsset.outDir = path.resolve('dist');

  if (config.entryJsFile) {
    rootAsset.ast = htmlParser(rootAsset.content);
    rootAsset.entryJsFilePath = path.resolve(rootAsset.rootDir, config.entryJsFile);
  } else {
    extractEntryJSFilePathFromEntryFile(rootAsset);
  }

  rootAsset.dependencyGraph = createDependencyGraph(rootAsset.entryJsFilePath);

  return rootAsset;
} 
```

它接收 CLI 工具传递给`bundler`函数的参数。唯一有趣的活动发生在`htmlParser`、`extractEntryJSFilePathFromEntryFile`和`createDependencyGraph`函数中。`fs`和`path`是节点模块，在这里记载为和`getRootDir`如其名。*注意:与`fs.readFileSync`同步读取文件不是很有性能，因为它是一个[阻塞](https://nodejs.org/de/docs/guides/blocking-vs-non-blocking/)调用，但是我们现在不太担心这个。*

当我们调用`htmlParser`时，它从我们的演示应用程序接收以下内容:

```
<html>
  <head>
    Hello, World
  </head>
  <body>
    <div id="app"></div>
    <script src="./index.js"></script>
  </body>
</html> 
```

`htmlParser`指的是模块 [`posthtml-parser`](https://github.com/posthtml/posthtml-parser) ，一个解析 HTML 并把它变成抽象语法树(AST)的工具。我们的 npm 命令`demo: maleta demo/index.html --entry index.js`帮助我们轻松找到相关条目 JavaScript 文件的路径。然而，如果`--entry`选项丢失，我们调用`extractEntryJSFilePathFromEntryFile`。

```
function extractEntryJSFilePathFromEntryFile(rootAsset) {
  const parsedHTML = htmlParser(rootAsset.content);

  rootAsset.ast = parsedHTML;
  parsedHTML.walk = walk;

  parsedHTML.walk(node => {
    if (node.tag === 'script') {
      if (node.attrs.src.endsWith('/index.js')) {
        rootAsset.entryJsFilePath = path.resolve(rootAsset.rootDir, node.attrs.src)
      }
    }

    return node;
  });

  if (!rootAsset.entryJsFilePath) throw Error('No JavaScript entry file has been provided or specified. Either specify an entry file or make sure the entry file is named \'index.js\'');
} 
```

这里唯一的区别是我们附加到 AST 上的 [`posthml`](https://github.com/posthtml/posthtml) 的`walk`方法。我们用它来遍历树，确保 HTML 文件链接到一个叫做`index.js`的 JavaScript 文件。

### 构建依赖图

我们的图表将是一个对象数组，列出了应用程序中的每个模块。每个对象将具有:

*   一个`id`
*   模块中的代码
*   原始文件名
*   该模块依赖项的相对文件路径的数组
*   具有相同依赖项 id 的对象。

`createDependencyGraph`做的第一件事是使用这个函数从入口 JavaScript 文件创建主资产:

```
function createJSAsset(filename) {
  const content = fs.readFileSync(filename, 'utf-8');
  const ast = babylon.parse(content, { sourceType: 'module' });

  const relativeFilePathsOfDependenciesArray = [];

  traverse(ast, {
    ImportDeclaration({ node }) {
      relativeFilePathsOfDependenciesArray.push(node.source.value)
    },
    CallExpression({ node })  {
      const { callee, arguments: args } = node;
      if (
        callee.name === 'require' &&
        args.length === 1 &&
        args[0].type === 'StringLiteral'

      ) {
        relativeFilePathsOfDependenciesArray.push(args[0].value)
      }
    }
  })

  const id = moduleID++;

  const { code } = transformFromAstSync(ast, null, {
    presets: ['@babel/env'],
    cwd: __dirname
  });

  return {
    id,
    code,
    filename,
    relativeFilePathsOfDependenciesArray,
    mapping: {}
  }
} 
```

`babylon`与 [babel](https://babeljs.io/) 使用的 JavaScript 解析器相同。它的`parse`方法将给定的代码作为 JS 程序运行，在第二个参数中，您传递一个 options 对象，告诉它是在处理一个模块还是脚本。它的输出是一个符合 [babel AST 格式](https://github.com/babel/babel/blob/master/packages/babel-parser/ast/spec.md)的 AST。我们使用它和巴别塔插件`traverse` ( [巴别塔遍历](https://github.com/babel/babel/tree/master/packages/babel-traverse))来查找所有的依赖引用。 [`ImportDeclaration`](https://github.com/babel/babel/blob/7f47cb65d0b7aea5f6c9a8ed0c255be1512738b7/packages/babel-parser/ast/spec.md#importdeclaration) 查找所有 ES 模块导入， [`CallExpression`](https://github.com/babel/babel/blob/7f47cb65d0b7aea5f6c9a8ed0c255be1512738b7/packages/babel-parser/ast/spec.md#callexpression) 查找每一个函数调用表达式，从中我们可以检查是否是用`require`关键字完成的。

下一个任务是解析文件中的 JavaScript 代码。`transformFromAstSync`是来自 [`babel/core`](https://babeljs.io/docs/en/next/babel-core.html#transformfromastsync) 模块的一个方法，它将我们的 AST 转换成将在浏览器中运行的最终代码。它还创建了一个源映射。在配置对象中，将工作目录设置为`maleta`很重要，否则任何文件路径都将被解析到运行 maleta 的目录，在我们的例子中是`aprender`。

一旦从入口 JavaScript 文件创建了主资产，就将其分配给`assetQueue`数组进行处理。这个数组是一个队列，它最终将包含代表应用程序中每个 JavaScript 文件的资产。每个资产及其依赖项之间的关系存储在一个名为`mapping`的对象中。该对象的每个属性都是每个依赖项的文件名及其 id。

### 创建束

```
function createBundle(entryFile, config) {
  let modules = '';
  let bundle;
  const rootAsset = createRootAssetFromEntryFile(entryFile, config);
  const bundlePath = path.resolve(rootAsset.outDir, 'index.js');
  const bundleHtml = htmlRender(rootAsset.ast);
  const bundleHtmlPath = path.resolve(rootAsset.outDir, 'index.html');

  // ...
} 
```

`createBundle`是我们的 CLI 用来启动捆绑流程的函数。`createRootAssetFromEntryFile`执行上面列出的所有步骤并返回一个`rootAsset`对象。由此，我们为输出文件创建文件路径。我们还使用`htmlRender`(实际上是 [`posthtml-render`](https://github.com/posthtml/posthtml-render) )将我们从入口 HTML 文件中抓取的 AST 转换成一个新的 HTML 树。下一步是迭代依赖图并创建捆绑代码，如下所示:

```
function createBundle(entryFile, config) {
  // ...

  rootAsset.dependencyGraph.forEach(mod => {
    modules += `${mod.id}: [
      function (require, module, exports) { ${mod.code} }, ${JSON.stringify(mod.mapping)},
    ],`;
  });

  bundle = `
    (function(modules) {
      function require(id) {
        const [fn, mapping] = modules[id];

        function localRequire(name) {
          return require(mapping[name]);
        }

        const module = { exports: {} };

        fn(localRequire, module, module.exports);

        return module.exports;
      }

      require(0);
    })({${modules}})
  `;

  // ...
} 
```

### 捆绑解释

这个包是一个[立即调用的函数表达式(life)](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)，一个 JavaScript 函数，一旦被定义就立即运行。我们将它赋给`bundle`变量，然后将`modules`对象作为参数传入。每个模块都是一个数组，其中一个函数执行该模块的代码作为其第一个元素，模块/依赖关系作为其第二个元素。

生命做的第一件事是创建一个`require`函数，它将一个`id`作为唯一的参数。在这个函数中，我们析构数组并访问每个模块的函数和映射对象。这些模块将有对相对文件路径的`require()`调用，有些可能调用相同的文件路径，即使它们引用不同的依赖关系。我们通过创建一个专用的本地`require`函数来处理这个问题，这个函数将文件路径转换成模块 id。

例如，在我们的演示应用程序中，生命周期结束时的`require(0)`调用结果如下:

```
function require(id) {
  const [fn, mapping] = modules[id];
  /* the value of fn */
    function (require, module, exports) {
      "use strict";
      var aprender = require('../src/aprender');
      var button = aprender.createElement('button', {
        children: ['Click Me!']
      });
      var component = aprender.createElement('div', {
        attrs: {
          id: 'root-component'
        },
        children: ['Hello, world!', button]
      });
      var app = aprender.render(component);
      aprender.mount(app, document.getElementById('app'));
    }
  /* the value of mapping */ 
  {"../src/aprender": 1}
} 
```

`require('../src/aprender');`真的是`localRequire('../src/aprender')`。在内部，`localRequire`进行这个递归调用`require(mapping['../src/aprender']`。`mapping['../src/aprender']`返回值`1`，这是入口 JavaScript 文件的唯一依赖项`id`。`require(1)`退货:

```
function require(id) {
  const [fn, mapping] = modules[id];
  /* the value of fn */
    function (require, module, exports) {
      "use strict";
      var createElement = require('./createElement');
      var render = require('./render');
      var mount = require('./mount');
      module.exports = {
        createElement: createElement,
        render: render,
        mount: mount
      };
    }

  /* the value of mapping */
  {"./createElement":2,"./render":3,"./mount":4}
} 
```

每次依赖关系中的代码进行`require`调用时，它都会以这种方式被析构。bundler 生命中剩下的代码是:

```
function localRequire(name) {
  return require(mapping[name]);
}

const module = { exports: {} };

fn(localRequire, module, module.exports);

return module.exports; 
```

`localRequire`包装我们上面解释过的递归调用，而`fn(localRequire, module, module.exports)`执行我们在`require`函数开始时析构的函数。所有来自相关模块的依赖项的导出都将存储在`module`对象中。在我们的演示应用程序中，`createElement`、`render`和`mount`所有的导出函数和所有这些导出的对象是`aprender`模块的值。

### 上菜捆绑

一旦包准备好了，我们就创建一个输出目录，为演示应用程序创建`index.js`和`index.html`文件，然后使用 [`http`](https://nodejs.org/api/http.html) 和 [`serve-static`](https://github.com/expressjs/serve-static) 服务它们。

```
function createBundle(entryFile, config) {

  //...

  // create the output directory if it does not exist
  if (!fs.existsSync(rootAsset.outDir)) {
    fs.mkdirSync(rootAsset.outDir);
  }

  // create output html and js files
  fs.writeFileSync(bundlePath, bundle);
  fs.writeFileSync(bundleHtmlPath, bundleHtml);

  // create server and serve files
  const serve = serveStatic(rootAsset.outDir); 
  const server = http.createServer( function onRequest(req, res) {
    serve(req, res, finalhandler(req, res));
  });

  server.listen(3000);
  console.log(`${chalk.bold('Now serving the application on')}  ${chalk.red('http://localhost:3000')}`);
} 
```

### 总结

我们创建的 bundler 绝不是完美的，并且无疑包含许多漏洞和需要改进的地方。然而，它是实用的，这是最重要的事情。在我们的项目中，我们已经到了可以在浏览器中查看应用程序的阶段。在下一篇文章中，我们将回到我们的 UI 框架，并添加允许我们创建更复杂的演示应用程序的功能。