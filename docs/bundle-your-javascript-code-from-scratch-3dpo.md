# 从头开始捆绑您的 Javascript 代码

> 原文：<https://dev.to/iggredible/bundle-your-javascript-code-from-scratch-3dpo>

# [T1】简介](#intro)

捆绑是构建现代 Javascript 应用不可或缺的一部分。Webpack、Rollup、package-bunder 是一些著名的捆扎机。在很大程度上，捆绑是一个神奇的过程:只需给捆绑器输入、输出，添加一些其他配置，然后*噗*！-突然你的 bundle.js 准备好了。

[![magic](img/32cd95ab56ca27ae6c68f80e017f653c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PmFlI5G6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6pkw4boytmpzuki9wq31.gif)

在这篇文章中，我将解释捆绑器是什么以及为什么使用它是一件好事——我们将从头开始创建一个。

# 什么是捆扎机，为什么我们需要它

> bundler 是一个工具，它将您的入口代码及其所有依赖项放在一个 JS 文件中。

我们为什么要用它？我们能不能只上传我们项目的全部文件和目录，而不经历额外的步骤？

这里有两个原因:

1.  Javascript 最初没有标准的/内置的模块系统。`import`和`export`语法是 ES6 中最新的约定。[并非所有浏览器都支持它](https://caniuse.com/#feat=es6-module)。
2.  最好将所有内容放在一个打包文件中。想象一个项目，其中我们有 5 个不同的 JS 文件。客户端将不得不向你的服务器(或者 CDN，或者两者都有)发出 **5 个请求**，顺便说一句，[更好的做法是](https://stackoverflow.com/questions/7445909/bundling-js-files-vs-cdn)将它们捆绑起来放在 CDN 中。)-但是如果我们的项目被打包在一个 JS 文件中，这仍然是客户可以避免的 4 个额外请求(客户只需要发出一个请求)。*更多的请求=更多的开销*。

我希望这些是想使用捆绑器的足够理由。让我们继续了解捆扎机的工作原理

理解某物如何工作的最好方法是建造/修补它。

# 建筑捆绑工

在我们开始之前，让我们看一下我们项目的基本情况。

[![bandler logo](img/794784ab73974db2a53dbfce68bb7354.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9YlsJf-F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t0tm22z42vxz0iskdgje.png)

介绍[乐队成员](https://github.com/iggredible/bandler)。你见过的最小的，最可爱的，最牛逼的 bundler(好吧，随便你怎么叫。这只是我给我的 bundler 起的名字)。

班德勒会有这样的结构:

`entry.js`->-`module1.js`->-`module2.js`

该条目将被称为`entry.js`。它将有一个依赖项`module1.js`，T1 有一个依赖项`module2.js`。

我们的项目将使用 [ES6 模块语法](https://alligator.io/js/modules-es6/) ( `import` / `export`)。我们的任务是将模块支持扩展到旧的浏览器。我们必须将 ES6 语法转换成所有/大多数浏览器都能理解的东西。

以下是应该如何做的 8 个步骤:

1.  读取`entry.js`的内容
2.  解析该内容并制作一个包含所有`import`声明的**列表**
3.  将步骤 1 中的内容从 ES6 传输到 ES5
4.  为每个依赖文件分配一个惟一的 ID，以便以后引用(例如，如果我们在条目中使用`import module1 from './module1.js'`，那么`./module1.js`就是一个依赖文件，我们将用一个惟一的 ID 来映射它)
5.  将步骤 2-4 中的所有信息放在一个对象中
6.  创建一个‘依赖图’(通过遍历所有依赖关系、每个依赖关系的所有依赖关系等等；重复步骤 1-5)
7.  将步骤 6 中的所有东西打包在一起
8.  庆祝吧，因为我们的捆绑完成了！🎊🙌

如果它看起来很复杂，不要担心，因为它不是。

## 开始项目

在这一节中，我们将进行设置:为我们的项目创建一个新目录，并安装一些库。

`mkdir bundler-playground && cd $_`

启动 npm 项目。

`npm init -y`

安装一些附加库:

1.  `@babel/parser`解析我们的代码并返回一个 AST 对象
2.  穿过我们的 AST 对象；这将有助于我们寻找所有的进口申报
3.  `@babel/core`至转运 ES6 - > ES5
4.  `resolve`获得每个依赖项的完整路径(例如:把`./module1.js`变成类似于`/User/iggy/project/bundler-playground/module1.js`的东西)

`npm install --save @babel/parser @babel/traverse @babel/core resolve`

在根目录下创建一个新的`index.js`，并添加导入这些家伙:

```
 const fs = require("fs");
 const path = require("path");
 const parser = require("@babel/parser");
 const traverse = require("@babel/traverse").default;
 const babel = require("@babel/core"); 
 const resolve = require("resolve").sync; 
```

Enter fullscreen mode Exit fullscreen mode

## 获取模块信息

在本节中，我们将:

1.  给特定的`filePath`分配唯一的 ID(稍后引用)
2.  获取该文件使用的所有依赖项(列出所有使用的`import`)
3.  透明是代码

这是这一部分的代码。

```
let ID = 0;

function createModuleInfo(filePath) {
  const content = fs.readFileSync(filePath, "utf-8");
  const ast = parser.parse(content, {
    sourceType: "module"
  });
  const deps = [];
  traverse(ast, {
    ImportDeclaration: ({ node }) => {
      deps.push(node.source.value);
    }
  });
  const id = ID++;
  const { code } = babel.transformFromAstSync(ast, null, {
    presets: ["@babel/preset-env"]
  });

  return {
    id,
    filePath,
    deps,
    code
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们用 [`readFileSync()`](https://nodejs.org/api/fs.html#fs_fs_readfilesync_path_options) 得到了文件内容。然后我们解析内容以获得 [AST](https://stackoverflow.com/questions/16127985/what-is-javascript-ast-how-to-play-with-it) 信息。一旦获取了 AST，我们就使用 [`ImportDeclaration`](https://github.com/babel/babel/blob/master/packages/babel-parser/ast/spec.md#importdeclaration) API 遍历 AST 并寻找所有导入实例。最后，我们使用巴别塔核心的 [`transformFromAstSync`](https://babeljs.io/docs/en/babel-core#transformfromastsync) 从 ES6 传输我们的代码。

对于 ID，我们使用一个简单的递增数字(最好使用随机 GUID，因为这是一个演示，`ID++`就可以了)

这样，我们就有了一个漂亮的模块信息，包括一个惟一的 ID、所有依赖项(所有导入)的列表以及模块内部的代码。接下来，我们为所有相关模块重复这个过程，以创建一个依赖图。

## 创建依赖图

依赖图是我们的应用程序中使用的相关模块的集合，从入口点开始。

这是这个部分的代码。

```
function createDependencyGraph(entry) {
  const entryInfo = createModuleInfo(entry);
  const graphArr = [];
  graphArr.push(entryInfo);
  for (const module of graphArr) {
    module.map = {};
    module.deps.forEach(depPath => {
      const baseDir = path.dirname(module.filePath);
      const moduleDepPath = resolve(depPath, { baseDir });
      const moduleInfo = createModuleInfo(moduleDepPath);
      graphArr.push(moduleInfo);
      module.map[depPath] = moduleInfo.id;
    });
  }
  return graphArr;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将在依赖图中使用数组类型。我们首先推送我们的参赛信息。

然后我们遍历依赖图元素(从条目开始)。

```
const baseDir = path.dirname(module.filePath);
const moduleDepPath = resolve(depPath, { baseDir });
const moduleInfo = createModuleInfo(moduleDepPath);
graphArr.push(moduleInfo); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用`path.dirname`和`resolve`来获取每个模块的完整路径，使用完整路径获取信息，并将该信息推入我们的依赖图数组。

注意这几行:

```
module.map = {};
...
module.map[depPath] = moduleInfo.id; 
```

Enter fullscreen mode Exit fullscreen mode

这里我们在我们的`moduleInfo`对象中添加了一个额外的属性`map`。该属性将在下一步中用作*查找*，以用唯一标识符映射每个模块。例如:

| 组件 | 身份证明 |
| --- | --- |
| entry.js | Zero |
| 模块 1.js | one |
| 模块 2.js | Two |
| 等等 | n |

最后，我们得到了整个项目中使用的所有依赖项的模块信息数组。

## 将它们全部打包在一起

现在我们有了依赖图，最后一步是将**和**打包在一起。

[![mickey packing](img/f5b46bcaf7de6641184467b8079756f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vgavpo6A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0rz8w9epr6il0ie6rea4.gif)T3】

```
function pack(graph) {
  const moduleArgArr = graph.map(module => {
    return `${module.id}: {
      factory: (exports, require) => {
        ${module.code}
      },
      map: ${JSON.stringify(module.map)}
    }`;
  });
  const iifeBundler = `(function(modules){
    const require = id => {
      const {factory, map} = modules[id];
      const localRequire = requireDeclarationName => require(map[requireDeclarationName]); 
      const module = {exports: {}};
      factory(module.exports, localRequire); 
      return module.exports; 
    } 
    require(0);
  })({${moduleArgArr.join()}})
  `;
  return iifeBundler;
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们在每个模块的代码上创建一个工厂模式。它通过一个`export`和`require`。请记住这两个论点。我们保留上一步的地图。

```
return `${module.id}: {
  factory: (exports, require) => {
    ${module.code}
  },
  map: ${JSON.stringify(module.map)}
  }`; 
```

Enter fullscreen mode Exit fullscreen mode

第二，我们创造了一种生活来一起运行整个依赖图。接下来的部分可能会令人困惑——最初我很难理解这一部分，但是耐心一点，它会有意义的！

```
 const iifeBundler = `(function(modules){
    const require = id => {
      const {factory, map} = modules[id];
      const localRequire = requireDeclarationName => require(map[requireDeclarationName]); 
      const module = {exports: {}};
      factory(module.exports, localRequire); 
      return module.exports; 
    } 
    require(0);
  })({${moduleArgArr.join()}})
  `; 
```

Enter fullscreen mode Exit fullscreen mode

1.  我们使用生命模式来限定变量的范围，这样它们就不会影响全局变量
2.  我们在前面部分创建的依赖图作为参数(`${moduleArgArr.join()}`)被传递
3.  这个依赖图在 IIFE 中以`modules`的形式传递
4.  我们创建了一个`require(id)`函数。该功能有两个作用:
    *   它通过`require(map[requireDeclarationName])`用其他依赖项的 ID 递归调用自己的。这可以翻译成类似于`require(1)`的东西——回想之前的映射函数，变成了`require('./module1.js')`
    *   当它运行`factory(module.exports, localRequire)`时，它执行步骤 1 (createModuleInfo)中的实际代码
    *   这个函数返回`module.exports`——虽然最初是空的(`{exports: {}}`)，但是运行`factory()`之后，这个`module.exports`的值就是我们之前创建的`factory`里面的`exports`值(想想吧)

## 代码回购

这个博客的最终代码可以在这里找到[来比较代码。](https://github.com/iggredible/bandler/tree/ce43fa000b16931e9518e94506a81b381eed764b)

完整的代码看起来会像这样:

```
const fs = require("fs");
const path = require("path");
const parser = require("@babel/parser"); // parses and returns AST
const traverse = require("@babel/traverse").default; // AST walker
const babel = require("@babel/core"); // main babel functionality
const resolve = require("resolve").sync; // get full path to dependencies

let ID = 0;

/*
 * Given filePath, return module information
 * Module information includes:
 * module ID
 * module filePath
 * all dependencies used in the module (in array form)
 * code inside the module
 */
function createModuleInfo(filePath) {
  const content = fs.readFileSync(filePath, "utf-8");
  const ast = parser.parse(content, {
    sourceType: "module"
  });
  const deps = [];
  traverse(ast, {
    ImportDeclaration: ({ node }) => {
      deps.push(node.source.value);
    }
  });
  const id = ID++;
  const { code } = babel.transformFromAstSync(ast, null, {
    presets: ["@babel/preset-env"]
  });

  return {
    id,
    filePath,
    deps,
    code
  };
}

/*
 * Given entry path,
 * returns an array containing information from each module
 */
function createDependencyGraph(entry) {
  const entryInfo = createModuleInfo(entry);
  const graphArr = [];
  graphArr.push(entryInfo);
  for (const module of graphArr) {
    module.map = {};
    module.deps.forEach(depPath => {
      const baseDir = path.dirname(module.filePath);
      const moduleDepPath = resolve(depPath, { baseDir });
      const moduleInfo = createModuleInfo(moduleDepPath);
      graphArr.push(moduleInfo);
      module.map[depPath] = moduleInfo.id;
    });
  }
  return graphArr;
}

/*
 * Given an array containing information from each module
 * return a bundled code to run the modules
 */
function pack(graph) {
  const moduleArgArr = graph.map(module => {
    return `${module.id}: {
      factory: (exports, require) => {
        ${module.code}
      },
      map: ${JSON.stringify(module.map)}
    }`;
  });
  const iifeBundler = `(function(modules){
    const require = id => {
      const {factory, map} = modules[id];
      const localRequire = requireDeclarationName => require(map[requireDeclarationName]); 
      const module = {exports: {}};

      factory(module.exports, localRequire); 
      return module.exports; 
    } 
    require(0);
  })({${moduleArgArr.join()}})
  `;
  return iifeBundler;
}

console.log("***** Copy code below and paste into browser *****");

/* create dependency graph */
const graph = createDependencyGraph("./entry.js"); // wherever your entry is
/* create bundle based on dependency graph */
const bundle = pack(graph);

console.log(bundle);
console.log("***** Copy code above and paste into browser *****"); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行`node ./index.js`，我们将得到类似
的结果

```
(function(modules){
    const require = id => {
      const {factory, map} = modules[id];
      const localRequire = requireDeclarationName => require(map[requireDeclarationName]);
      const module = {exports: {}};

      factory(module.exports, localRequire);
      return module.exports;
    }
    require(0);
  })({0: {
      factory: (exports, require) => {
        "use strict";

var _module = _interopRequireDefault(require("./module1.js"));

var _module2 = _interopRequireDefault(require("./module2.js"));

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { "default": obj }; }

(0, _module["default"])();
(0, _module2["default"])();
      },
      map: {"./module1.js":1,"./module2.js":2}
    },1: {
      factory: (exports, require) => {
        "use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});
exports["default"] = void 0;

var _module = _interopRequireDefault(require("./module2.js"));

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { "default": obj }; }

var module1 = function module1() {
  (0, _module["default"])();
  console.log("hello from module1!");
};

var _default = module1;
exports["default"] = _default;
      },
      map: {"./module2.js":3}
    },2: {
      factory: (exports, require) => {
        "use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});
exports["default"] = void 0;

var module2 = function module2() {
  console.log("Hello from module2!");
};

var _default = module2;
exports["default"] = _default;
      },
      map: {}
    },3: {
      factory: (exports, require) => {
        "use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});
exports["default"] = void 0;

var module2 = function module2() {
  console.log("Hello from module2!");
};

var _default = module2;
exports["default"] = _default;
      },
      map: {}
    }})(function(modules){
    const require = id => {
      const {factory, map} = modules[id];
      const localRequire = requireDeclarationName => require(map[requireDeclarationName]);
      const module = {exports: {}};

      factory(module.exports, localRequire);
      return module.exports;
    }
    require(0);
  })({0: {
      factory: (exports, require) => {
        "use strict";

var _module = _interopRequireDefault(require("./module1.js"));

var _module2 = _interopRequireDefault(require("./module2.js"));

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { "default": obj }; }

(0, _module["default"])();
(0, _module2["default"])();
      },
      map: {"./module1.js":1,"./module2.js":2}
    },1: {
      factory: (exports, require) => {
        "use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});
exports["default"] = void 0;

var _module = _interopRequireDefault(require("./module2.js"));

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { "default": obj }; }

var module1 = function module1() {
  (0, _module["default"])();
  console.log("hello from module1!");
};

var _default = module1;
exports["default"] = _default;
      },
      map: {"./module2.js":3}
    },2: {
      factory: (exports, require) => {
        "use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});
exports["default"] = void 0;

var module2 = function module2() {
  console.log("Hello from module2!");
};

var _default = module2;
exports["default"] = _default;
      },
      map: {}
    },3: {
      factory: (exports, require) => {
        "use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});
exports["default"] = void 0;

var module2 = function module2() {
  console.log("Hello from module2!");
};

var _default = module2;
exports["default"] = _default;
      },
      map: {}
    }}) 
```

Enter fullscreen mode Exit fullscreen mode

复制/粘贴到浏览器中，你会看到

```
Hello from module2!
hello from module1!
Hello from module2! 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你！我们刚刚建造了一个完整的捆扎机...从零开始！！

[![traveling with giant luggage](img/7c985f829b7c127f586013d76a89a301.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0xOKlQ_t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ppozpb2ciyp2ciitsih7.gif)

## 奖金

除了创建一个 ES6 捆绑器，我还试图创建一个捆绑 CJS 和 ES6 的捆绑器，[捆绑器](https://github.com/iggredible/bandler) ( [NPM](https://www.npmjs.com/package/bandler) )

我在这里不会讲得太深——但是除了使用 babel parser 和 babel traverse 之外，我还使用了 [`detective`](https://www.npmjs.com/package/detective) 库，专门搜索并列出项目中所有 CJS 需要的(例如:`require('./your/lib.js')`)实例。我看到巴别塔这里没有 CJS 语法声明。

你能想出其他方法让 CJS 和 ES6 捆绑吗？

## 资源、链接等

大众捆扎机

*   [浏览确认](http://browserify.org/)
*   [网络包](https://webpack.js.org/)
*   [rollup](https://rollupjs.org/guide/en/)
*   [包裹捆扎机](https://github.com/parcel-bundler/parcel)

这篇文章的灵感

*   [迷你包](https://github.com/ronami/minipack/blob/master/src/minipack.js)
*   [wbpck-bundler](https://github.com/adamisntdead/wbpck-bundler)

捆绑器阅读

*   [捆扎机概述](https://medium.com/@gimenete/how-javascript-bundlers-work-1fc0d0caf2da)
*   [创建自己的捆绑器](https://www.freecodecamp.org/news/anatomy-of-js-module-systems-and-building-libraries-fadcd8dbd0e/) -上面提到的 wbp CK-捆绑器的创建者
*   [热门 js 打捆机小名单](https://dev.to/kayis/4-javascript-bundlers-2g4b)
*   (又一个)[构建工具列表](https://survivejs.com/webpack/appendices/comparison/)