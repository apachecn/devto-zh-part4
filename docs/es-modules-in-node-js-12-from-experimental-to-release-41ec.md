# Node.js 12 中的 ES 模块，从实验到发布

> 原文：<https://dev.to/bnevilleoneill/es-modules-in-node-js-12-from-experimental-to-release-41ec>

[![](img/6e1675d4f3242acdb22e1ee9223b98be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gD8nth9G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zx5vj9xz27j3ium81k6d.jpeg)

不同形式的模块化在 JavaScript 生态系统中已经存在多年了。开发人员使用定义良好的规范(如 AMD 或 CommonJS)以及简单的编码模式(如揭示模块模式)来获得良好模块化解决方案的好处。

模块可以在浏览器中的客户端使用，也可以在 Node.js 中的服务器端使用。有时使用 Babel 等工具将代码从一种模块格式转换为另一种模块格式。所有这些导致了混乱而复杂的 JavaScript 模块状态。

输入 es 模块—更具体地说，是 Node.js 中的 ES 模块。

> 提示:本文主要关注 Node.js 中的 es 模块。查看"[common js vs AMD vs require js vs ES6 Modules](https://medium.com/computed-comparisons/commonjs-vs-amd-vs-requirejs-vs-es6-modules-2e814b114a0b)"可以很好地比较非 Node.js 特有的模块系统。

## 简史

让我们看一下 es 模块支持的一些重要里程碑:

*   **2015 年 6 月:** ES 模块在 ECMAScript 的 [ES2015 规范](http://www.ecma-international.org/ecma-262/6.0/index.html)中定义。

*   【2015 年 6 月–2017 年 9 月:各大浏览器增加对隐藏在开发者旗帜后面的 ES 模块的实验性支持。使用 ES 模块开发 JavaScript 的主要方法是使用 Babel 之类的工具编译代码。

*   【2017 年 9 月: [Node.js v8.5](https://nodejs.org/de/blog/release/v8.5.0/) 包含对 es 模块的实验支持。

*   【2017 年 9 月-2018 年 5 月:各大浏览器开始支持无开发者标志的 ES 模块规范，包括:

1.  2017 年 9 月 5 日，Chrome 61
2.  **Safari 11** ，2017 年 9 月 18 日
3.  2018 年 5 月 8 日，火狐 60

*   【2018 年 10 月:创建新的模块实施计划。该计划包括用新的实现替换当前实验性实现的几个阶段，从第一天起就遵循三个指导原则:

1.  符合 ES 规范
2.  节点应该尽可能用浏览器的方式做事
3.  不要破坏现有的 CommonJS 模块

> 提示:Node.js 模块团队已经为新的实现提供了一套更详细的指导原则。

*   【2019 年 10 月(暂定):节点 12 预计进入长期支持。[根据官方计划](https://github.com/nodejs/modules/blob/master/doc/plan-for-new-modules-implementation.md#plan-for-new-modules-implementation)，目标是在此时发布对 ES 模块的全面支持。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

### 为什么完整的 ES 模块支持里程碑对 Node.js 如此重要？

有几个原因。首先，所有主流浏览器都已经支持 ES 模块——你可以[在这里](https://caniuse.com/#feat=es6-module)亲眼看看。在 Node.js 中支持服务器端的 ES 模块将允许全栈开发人员自然地为客户机和服务器编写模块化的、可重用的 JavaScript。

另一方面，Node.js 中的实验性特性在未来版本中可能会发生非向后兼容的变化或删除。也就是说，实验性 ES 模块支持已经在 Node 中存在了几年，预计在 2019 年 10 月之前不会发生显著变化。

## Node.js 中的  模块:当前状态

### CommonJS 模块

Node.js 中的模块目前(2019 年年中，撰写本文时)事实上的标准是 CommonJS。CommonJS 模块是在 normal 中定义的。js 文件使用`module.exports`。模块可以稍后在其他。带有`require()`功能的 js 文件。比如:

```
// foo.js
module.exports = function() { 
  return 'Hello foo!';
}

// index.js
var foo = require('./foo');
console.log(foo()); // Hello foo! 
```

Enter fullscreen mode Exit fullscreen mode

使用 Node 运行这个带有`node index.js`的例子。

### ES 模块

从 Node v8.5 开始，开发人员已经能够使用`--experimental-modules`标志运行对 ES 模块规范的各种支持。从节点 v12.4 开始，可以在中定义模块。mjs 文件(或。js 文件[特定情况下](https://nodejs.org/api/esm.html)。例如:

```
// foo.mjs
export function foo() { 
  return 'Hello foo!'; 
}

// index.mjs
import { foo } from './foo.mjs';
console.log(foo()); // Hello foo! 
```

Enter fullscreen mode Exit fullscreen mode

使用 Node 运行这个带有`node --experimental-modules index.mjs`的例子。

### 在同一应用程序中使用 CommonJS 和 ES 模块

在某些方面，在浏览器中支持 es 模块可能比在 Node 中支持 ES 模块简单一些，因为 Node 已经有了一个定义良好的 CommonJS 模块系统。幸运的是，社区在确保开发者可以同时使用这两种类型的模块，甚至可以从一种模块导入到另一种模块方面做了出色的工作。

例如，假设我们有两个模块。第一个是 CommonJS 模块，第二个是 ES 模块(注意不同的文件扩展名):

```
// cjs-module-a.js
module.exports = function() {
  return 'I am CJS module A';
};

// esm-module-a.mjs
export function esmModuleA() {
  return 'I am ESM Module A';
};
export default esmModuleA; 
```

Enter fullscreen mode Exit fullscreen mode

要在 ES 模块脚本中使用 CommonJS 模块(注意。`import`关键字的 mjs 扩展和使用:

```
// index.mjs
import esmModuleA from './esm-module-a.mjs';
import cjsModuleA from './cjs-module-a.js';
console.log(`esmModuleA loaded from an ES Module: ${esmModuleA()}`);
console.log(`cjsModuleA loaded from an ES Module: ${cjsModuleA()}`); 
```

Enter fullscreen mode Exit fullscreen mode

使用 Node 运行这个带有`node --experimental-modules index.mjs`的例子。

要在标准 CommonJS 脚本中使用 ES 模块(注意。`require()`函数的 js 扩展和使用:

```
// index.js
// synchronously load CommonJS module
const cjsModuleA = require('./cjs-module-a');
console.log(`cjsModuleA loaded synchronously from an CJS Module: ${cjsModuleA()}`);

// asynchronously load ES module using CommonJS
async function main() {
  const {esmModuleA} = await import('./esm-module-a.mjs');
  console.log(`esmModuleA loaded asynchronously from a CJS module: ${esmModuleA()}`);
}
main(); 
```

Enter fullscreen mode Exit fullscreen mode

这些例子提供了如何在同一个应用程序中一起使用 CommonJS 和 es 模块的基本演示。请参阅 Gil Tayar 的“[NodeJS 中的本地 es 模块:现状和未来方向，第一部分](https://medium.com/@giltayar/native-es-modules-in-nodejs-status-and-future-directions-part-i-ee5ea3001f71)”，深入了解 CommonJS 和 ES 模块互操作性。

## Node.js 中的  模块:未来状态

在撰写本文时，新模块实施计划正处于第三阶段(也是最后一个阶段)。第 3 阶段计划在节点 12 LTS 发布的同时完成，届时 ES 模块支持将在没有`-experimental-modules`标志的情况下可用。

阶段 3 可能会带来一些大的改进来完善 ES 模块的实现。

### 装载机解决方案

开发者希望模块加载系统灵活且功能齐全。以下是 Node.js 模块加载器解决方案开发中的一些关键特性:

*   **代码覆盖/插装:**使开发者工具能够检索关于 CJS 和 ESM 模块使用的数据。

*   **可插拔加载器:**允许开发者在他们的包中包含加载器插件，这些插件可以定义从特定的文件扩展名或 mimetypes 加载模块的新行为，甚至是没有扩展名的文件。

*   **运行时加载器:**允许导入语句中引用的文件在导入时(运行时)被传输。

*   **模块的任意来源:**允许从文件系统以外的来源加载模块(例如，从 URL 加载模块)。

*   模拟模块:允许在测试时用模拟来替换模块。

您可以[在此](https://github.com/nodejs/modules/#features)查看完整列表。

### `"exports"`物体在`package.json`

虽然命名和语法不是最终的，但是这里的想法是在`package.json`文件中的某个地方有一个对象，允许包为包内的不同组件提供“漂亮的”入口点。以这个`package.json`作为一个可能实现的例子:

```
{
  "name": "@myorg/mypackage",
  "version": "1.0.0",
  "type": "module",
  "main": "./dist/index.js",
  "exports": {
    ".": "./src/mypackage.mjs",
    "./data": "./data/somedir/someotherdir/index.mjs"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

开发者可以像这样导入`@myorg/mypackage`的数据组件:

```
import { MyModule } from '@myorg/mypackage/data 
```

Enter fullscreen mode Exit fullscreen mode

### 用包名引用包根

当从同一个包中的另一个模块中引用一个模块时，您可能会以如下所示的大量回溯结束:

```
import coolcomponent from '../../../coolcomponent/module.js 
```

Enter fullscreen mode Exit fullscreen mode

如果实现了这一改变，那么回溯可以被替换为对`package.json`中定义的包名的引用。新代码将如下所示:

```
import coolcomponent from 'mypackage/coolcomponent/module.js 
```

Enter fullscreen mode Exit fullscreen mode

### 支持双 ESM/CommonJS 包

允许 npm 包同时包含 CJS 和 es 模块对于确保有一条向后兼容的、开发人员友好的路径从 CommonJS 迁移到 ES 模块非常重要。这通常被称为“双模式”支持。

双模支持的现状方法是将现有的`package.json`中的`main`入口点指向一个 CommonJS 入口点。如果一个 npm 包包含 ES 模块并且开发者想要使用它们，他们需要使用深度导入来访问那些模块(例如，`import 'pkg/module.mjs'`)。这是可能随 Node.js 12 LTS 一起发布的双模式解决方案。

还有其他一些支持双模式的提议。这个[被广泛争论的提议](https://github.com/nodejs/modules/issues/273)包括一些选项，使开发者更容易用两个独立的实现(ESM 和 CJS)来发布包，但是这个提议没有达成共识。

一个更新的关于 ESM 的`require`的[提议提出了一个不同的方法，允许开发者用`require()`解析 ES 模块。该提案仍然开放，但已被搁置，不太可能被纳入节点 12 LTS。](https://github.com/nodejs/modules/issues/299)

## 你好 ES 模块，再见 CommonJS？

虽然目标是 ES 模块最终取代 Node.js 中的 CommonJS 模块，但是没有人知道未来会怎样——也不知道 CommonJS 模块支持多久会消失。但有一点是肯定的:节点开发人员已经花费了相当多的时间和精力来确保无缝过渡到没有 CommonJS 的未来。

他们在确保两种模块类型之间的互操作性，同时尽量不引入太多新的双模式 API 方面做了出色的工作，一旦关键部分完成迁移，并且到了从 Node 中移除对 CommonJS 的支持的时候，这些新的双模式 API 就会变得毫无用处。

那么什么时候会从 Node.js 中移除 CommonJS 呢？让我们做一个疯狂的、毫无根据的预测，假设节点 18 有一个`--experimental-no-commonjs-modules`，节点 20 是最后的日落。跨浏览器、服务器和 JavaScript 运行的任何地方的模块化 JavaScript 的未来是令人兴奋的！

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

Node.js 12 中的 ES 模块从实验到发布的帖子最早出现在[的博客](https://blog.logrocket.com)上。