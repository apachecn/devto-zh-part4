# 前端中的嵌套依赖关系

> 原文：<https://dev.to/open-wc/nested-dependencies-in-frontend-558c>

所以你有了这个很棒的想法，现在你想实际去做。我很确定你不想从头开始，所以让我们使用现有的开源包。

如果你想合作，所有的代码都在 [github](https://github.com/daKmoR/nested-dependencies-in-frontend) 上。

对于我们的例子，我们想使用 lit-element 和 lit-html。

```
mkdir nested-dependecies-in-frontend
cd nested-dependecies-in-frontend
npm install lit-element lit-html@1.0.0 --save-exact 
```

> 注意:我们在这里故意使用固定版本。

然后我们将两个包都加载到我们的`main.js`中。

```
import { LitElement } from "lit-element";
import { html } from "lit-html";

console.log(LitElement);
console.log(html); 
```

为了了解我们的应用程序有多大，我们想创建一个汇总包。首先，安装汇总:

```
npm install -D rollup 
```

然后创建一个`rollup.config.js`

```
export default {
  input: "main.js",
  output: {
    file: "bundle.js",
    format: "iife"
  },
}; 
```

接下来，将`"build": "rollup -c rollup.config.js && du -h bundle.js"`添加到 package.json 的`scripts`块中，这样我们就可以轻松地构建文件并输出其文件大小。
让我们通过`npm run build` :)
来运行它

```
(!) Unresolved dependencies
https://rollupjs.org/guide/en#warning-treating-module-as-external-dependency
lit-element (imported by main.js)
lit-html (imported by main.js) 
```

哦！没用！😭

好吧，我以前听过这个...我们需要添加一些插件，以便 Rollup 能够理解节点解析(即类似于`import { html } from 'lit-html'`的裸模块说明符)的工作方式。

```
npm i -D rollup-plugin-node-resolve 
```

```
+ import resolve from "rollup-plugin-node-resolve";
+
   export default {
    input: "main.js",
    output: {
      file: "bundle.js",
      format: "iife"
    },
+  plugins: [resolve()]
  }; 
```

```
$ npm run build
# ...
created bundle.js in 414ms
96K     bundle.js 
```

所以这看起来不错。💪

### 如果有人偏爱纱线会怎样？

先安装一个纱线，然后再构建，结果应该是一样的，对吗？

```
$ yarn install
$ yarn build
# ...
created bundle.js in 583ms
124K    bundle.js 
```

哇！这是意料之外的——`yarn`版本的内存是 124K，而`npm`是 96K。
看来纱线构建包含一些额外的文件...也许一个包裹被复制了？

```
$ yarn list --pattern lit-*
├─ lit-element@2.2.0
│  └─ lit-html@1.1.0
└─ lit-html@1.0.0 
```

是的，`lit-html`版本`1.0.0`和`1.1.0`都装了。原因很可能是当我们用上面的`npm install --save-exact lit-html@1.0.0`命令安装`lit-html`时，我们将`lit-html`固定到了根依赖中的`1.0.0`版本。

虽然`npm`似乎可以很好地删除重复数据，但我觉得使用`npm`并不安全，因为如果依赖关系树变得更大，npm 也会喜欢安装嵌套依赖关系。

```
$ npm ls lit-element lit-html
├─┬ lit-element@2.2.0
│ └── lit-html@1.0.0  deduped
└── lit-html@1.0.0 
```

另外，特别是当你使用一些 beta(例如`0.x.x`)依赖时，这变得非常棘手。在这种情况下，[塞弗](https://semver.org/#spec-item-4)说每一次`0.x.0`释放都意味着一次[突破性的改变](https://semver.org/#how-should-i-deal-with-revisions-in-the-0yz-initial-development-phase)。这意味着`0.8.0`被视为与`0.9.0`不兼容。因此，即使您使用的 API 在两个版本中都工作得很好，您也总是会得到嵌套的依赖关系，这可能会悄悄地破坏您的应用程序。例如，终端上将没有警告或信息😱

### 节点解析如何工作

在 nodejs 中，当您使用一个裸说明符导入一个文件时，例如,`import { LitElement } from "lit-element";`节点的模块解析器函数获取字符串`lit-element`，并开始在`module.paths`中列出的所有目录中搜索导入模块，您可以像检查节点 REPL 中的任何其他值一样检查它:

```
$ node
module.paths
[
  '/some/path/nested-dependencies-in-frontend/node_modules',
  '/some/path/node_modules',
  '/some/node_modules',
  '/node_modules',
]
# unimportant folders are hidden here 
```

基本上，node 查看每个`node_modules`文件夹，从模块的父目录开始，沿着文件树向上移动，直到找到与模块说明符匹配的目录名(在我们的例子中是`lit-element`)。解析算法总是从当前模块的父目录开始，所以它总是相对于文件的导入位置。如果我们从 lit-element 的目录中检查`module.paths`，我们会看到一个不同的列表。

```
$ cd node_modules/lit-element
$ node
module.paths
[
  '/some/path/nested-dependencies-in-frontend/node_modules/lit-element/node_modules',
  '/some/path/nested-dependencies-in-frontend/node_modules',
  '/some/path/node_modules',
  '/some/node_modules',
  '/node_modules',
] 
```

现在我们可以理解什么是节点的嵌套依赖。每个模块都可以有自己的`node_modules`目录，令人讨厌的*，并且在那个模块的文件中引用的导入总是首先在它们最近的`node_modules`目录中查找...*

 *| 节点上嵌套依赖的优点 | 前端嵌套依赖的缺点 |
| --- | --- |
| 每个包都可以有自己的依赖版本 | 运送相同的代码两次意味着更长的下载和处理时间 |
| 包不受应用程序中其他包的依赖关系的影响 | 如果相同的代码从两个不同的位置导入两次，那么东西可能会损坏(例如，通过 [WeakMaps](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap) 或 singletons 进行性能优化) |
| 访问许多额外文件无需支付“高额费用”。 | 检查文件是否存在是一个额外的请求 |
| 在服务器上，你通常不太关心有多少额外的代码(文件大小) | 总之，你的网站会变慢 |

### 出现了问题

简而言之，偏好嵌套的自动模块解析对于前端来说可能是危险的。

*   我们关心加载和解析性能
*   我们关心文件的大小
*   有些包必须是单件的(即在模块图中是唯一的)才能在我们的应用程序中正常工作
    *   例子包括`lit-html`和`graphql`
*   我们应该完全控制最终出现在客户浏览器上的内容

节点样式的模块解析是为服务器端环境设计的，当在浏览器中采用时，可能会将这些问题变成严重的问题。
<abbr title="In my humble opinion">IMHO</abbr> 即使节点解析在技术上是可能的，为一个复杂的数据网格多次加载代码绝不应该是我们作为前端开发人员的目标。

### 方案

令人欣慰的是，我们今天可以使用这些问题的解决方案，并且即将出现的提案将完全消除未来对这种变通办法的需要。

#### 今天成功了

下面是一些在前端代码中使用裸模块说明符的技巧:

*   确保您的依赖关系树中的模块都使用它们的公共依赖关系的相似版本范围
*   尽可能避免固定特定的包版本(就像我们在上面用`npm i -S lit-html@1.0.0`做的那样)
*   如果您正在使用`npm`:
    *   安装软件包后运行`npm dedupe`删除嵌套的重复项。
    *   您可以尝试删除您的`package-lock.json`并进行全新安装。有时它神奇地帮助了🧙‍♂️
*   如果您正在使用`yarn`:
    *   考虑使用 [yarn resolutions](https://yarnpkg.com/lang/en/docs/selective-version-resolutions/) 来指定任何复制包的首选版本

#### 展望未来

如果我们能够准确地告诉 JavaScript 环境(即浏览器)在哪个`path`找到某个字符串指定的文件，我们就不需要节点样式的解析或编程时的重复数据删除例程。
我们将编写类似这样的东西，并将其传递给浏览器，以指定哪些路径映射到哪些包:

```
{  "lit-html":  "./node_modules/lit-html.js",  "lit-element":  "./node_modules/lit-element.js"  } 
```

使用这个导入映射来解析包路径意味着总是只有一个版本的`lit-html`和`lit-element`，因为全局环境已经知道在哪里可以找到它们。

幸运的是，✨，这已经是一个被提议的名为[导入地图](https://github.com/WICG/import-maps)的规范了。因为它是为浏览器设计的，所以根本不需要做任何转换！你只需提供地图，开发时不需要任何构建步骤？

听起来很疯狂😜？我们来试试吧！🤗

> 注意:请注意，这是一个实验性的 API 提议，它还没有最终确定或被实现者接受。

它目前只能在 Chrome 75+中工作，在一面旗帜后面。
所以在地址栏输入`chrome://flags/`，然后搜索`Built-in module infra and import maps`并启用。
下面是它的直接链接:。

#### 在浏览器中使用导入地图

为了使用导入映射，让我们创建一个`index.html`文件。

```
<html lang="en-GB">
<head>
  <script type="importmap">
    {
      "imports": {
        "lit-html": "./node_modules/lit-html/lit-html.js",
        "lit-html/": "./node_modules/lit-html/",
        "lit-element": "./node_modules/lit-element/lit-element.js",
        "lit-element/": "./node_modules/lit-element/"
      }
    }
  </script>
  My app
</head>

<body>
  <crowd-chant>
    <span slot="what">Bare Imports!</span>
    <span slot="when">Now!</span>
  </crowd-chant>

  <script type="module" src="./main.js"></script>
</body>

</html> 
```

并调整`main.js`。

```
import { html, LitElement } from "lit-element";

class CrowdChant extends LitElement {
  render() {
    return html`
      <h2>What do we want?</h2>
      <slot name="what"></slot>
      <h2>When do we want them?</h2>
      <time><slot name="when">Now!</slot></time>
    `;
  }
}

customElements.define("crowd-chant", CrowdChant); 
```

保存文件，然后通过在同一个目录中运行`npx http-server -o`在本地提供它。
这将打开 [http://localhost:8080/](http://localhost:8080/) ，在这里您将看到您的定制元素呈现在屏幕上。🎉

这是什么妖术🔮？在没有任何捆绑器、工具或构建步骤的情况下，我们编写了一个组件化的应用程序，使用了我们已经熟悉并喜爱的简单说明符。

让我们来分解一下:

```
import { html } from 'lit-html';
// will actually import "./node_modules/lit-html/lit-html.js"
// because of
// "lit-html": "./node_modules/lit-html/lit-html.js",

import { repeat } from 'lit-html/directives/repeat.js'
// will actually import "./node_modules/lit-html/directives/repeat.js"
// beacause of
// "lit-html/": "./node_modules/lit-html/", 
```

所以这意味着

1.  您可以直接导入包，因为包名称映射到特定的文件
2.  您可以导入子目录和文件，因为`packageName + '/'`被映射到它的目录
3.  从子目录导入文件时，您必须*而不是*省略`.js`

#### 这一切对我的生产构建意味着什么？

重要的是要再次指出，这仍然是实验性的技术。在任何情况下，您可能仍然希望使用 Rollup 之类的工具为生产站点进行优化构建。我们正在共同探索这些新的 API 将为我们的网站和应用带来什么。潜在的提议仍然不稳定，但这不应该阻止我们实验并从中提取效用。毕竟，我们大多数人都习惯于使用`babel`来启用 decorators 之类的实验性语法，尽管在撰写本文时该提议至少有四种风格。

如果您现在想尝试在不受支持的浏览器中导入地图，您将需要一个构建步骤或一个运行时解决方案，如 systemjs。对于 build-step 选项，您将使用尊重您的`import map`的东西来替换`rollup-plugin-node-resolve`，而不是使用节点解析。

如果你可以指向你的`index.html`，让它知道你的入口点是什么，是否有一个导入映射，这不是很好吗？

这就是为什么在 [open-wc](https://open-wc.org) 上，我们会用我们的`rollup-plugin-index-html`发布对导入地图的实验性支持。

您可以在 dev.to 上阅读所有相关内容。请关注此处的公告😉。

在[推特](https://twitter.com/openwc)上关注我们，或者在我的个人[推特](https://twitter.com/dakmor)上关注我。
请务必在[open-wc.org](https://open-wc.org)查看我们的其他工具和建议。

感谢[本尼](https://dev.to/bennypowers)和[拉斯](https://github.com/LarsDenBakker)的反馈，并帮助将我的涂鸦变成一个后续的故事。*