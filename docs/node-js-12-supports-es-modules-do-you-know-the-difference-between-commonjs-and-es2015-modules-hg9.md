# Node.js 12 支持 ES 模块。你知道 CommonJS 和 ES2015+模块的区别吗？

> 原文：<https://dev.to/exacs/node-js-12-supports-es-modules-do-you-know-the-difference-between-commonjs-and-es2015-modules-hg9>

如果您是 Node.js 开发人员，编写 Node.js 应用程序或库，您可能知道 Node.js 12 支持 ECMAScript 标准模块！~~(从今年 10 月 LTS 发布开始，该功能可能会稳定，不会有任何实验标志)~~。**编辑:Node.js 12 并没有放弃对`--experimental-modules`标志的需求。更多内容请看[官方文档](https://nodejs.org/docs/latest-v12.x/api/esm.html#esm_enabling)**

* * *

你知道 CommonJS 和 es 模块有什么区别吗？

## Pre Node.js 12。CommonJS(又名 CJS)

### 导出和导入

我们有两种导出方式，命名导出和默认导出

```
// commonjs/named.js
module.exports.sayHello = function sayHello (name) { return `Hello ${name}` }

// commonjs/default.js
module.exports = function sayHello (name) { return `Hello ${name}` } 
```

还有两种导入方式:

```
// index.js
// Named import without changing the name
const { sayHello } = require('./commonjs/named')

// Named import changing the name
const { sayHello: say2 } = require('./commonjs/named')

// Default import
const sayDefault = require('./commonjs/default')

console.log(sayHello('World'))
console.log(say2('World'))
console.log(sayDefault('World')) 
```

出口和进口都有一些类似的选择，但它们是等价的:

```
// Named import
const say2 = require('./commonjs/named').sayHello

// Named export
module.exports = {
  sayHello: function sayHello (name) {
    return `Hello ${name}`
  }
} 
```

### 光秃的小路。Node.js 中的模块解析

Node.js 中的 **`require`接受一个*裸路径*，这样我们就可以从`node_modules`目录中声明/导出库:** 

```
// node_modules/my-lib/package.json
{ "main": "index.js" }

// node_modules/my-lib/index.js
module.exports.sayHello = function sayHello (name) { return `Hello ${name}` } 
```

并导入它们(Node.js 解析`my-lib`到`./node_modules/my-lib/index.js` ):

```
// index.js
const say3 = require('my-lib')
console.log(say3('World')) 
```

## 未来。ES 模块(又称 ESM)

### 导出和导入

像在 CommonJS 中一样，有两种导出方式:命名和默认。

```
// esm/named.js
export function sayHello (name) { return `Hello ${name}` }

// esm/default.js
export default function sayHello (name) { return `Hello ${name}` } 
```

还有两种导入方式:

```
// index2.js
// Named import without changing the name
import { sayHello } from './esm/named.js'

// Named import changing the name
import { sayHello as say2 } from './esm/named.js'

// Default import
import sayDefault from './esm/default.js'

console.log(sayHello('World'))
console.log(say2('World'))
console.log(sayDefault('World')) 
```

请注意，以下“替代”存在，但不等同于命名导出。**不要把它们当作等同于命名出口**

```
// This is NOT a named export!!
export default {
  sayHello: function (name) {
    return `Hello ${name}`
  }
}

// This will not work with the above!
import { sayHello } from './esm/variation.js'

// This works but is NOT a named import
import say from './esm/variation.js'
const { sayHello } = say 
```

### 光秃秃的小路。模块名称解析

Node.js 12 正确解析*裸路径*:

```
// node_modules/my-esm-lib/package.json
{ "main": "index.js" }

// node_modules/my-esm-lib/index.js
export default function sayHello (name) { return `Hello ${name}` } 
```

并导入它们(Node.js 解析`my-esm-lib`到`./node_modules/my-esm-lib/index.js` ):

```
// index2.js
import say3 from 'my-esm-lib'
console.log(say3('World')) 
```

# 互通性

## 将 CJS 模块导入 ESM 项目

依赖项仍在 CommonJS:

```
// commonjs/named.js
module.exports.sayHello = function sayHello (name) { return `Hello ${name}` }

// commonjs/default.js
module.exports = function sayHello (name) { return `Hello ${name}` } 
```

所以你需要知道当你 ~~`require`~~ `import`它们到一个 ESM 文件时会发生什么。

CJS 的所有`module.exports`对象将被转换成一个**ESM 默认出口**。导入 CommonJS 模块时，不能使用 ESM 命名导出。

CJS 的所有`module.exports`对象将被转换成一个**ESM 默认出口**。导入 CommonJS 模块时，不能使用 ESM 命名导出。

> 📝从 [Node.js 路线图计划](https://github.com/nodejs/modules/blob/master/doc/plan-for-new-modules-implementation.md):现状是当前`--experimental-modules`行为:`import`只有 CommonJS 默认导出，所以`import _ from 'cjs-pkg'`没有`import { shuffle } from 'cjs-pkg')`

```
// index.mjs
// "Fake named import" without changing the name
import named from './commonjs/named.js'
const { sayHello } = named

// "Fake named import" changing the name
import named2 from './commonjs/named.js'
const { sayHello: say2 } = named2

// Default import
import sayDefault from './commonjs/default.js'

console.log(sayHello('World'))
console.log(say2('World'))
console.log(sayDefault('World')) 
```

### 备选:做一个中间模块。

通过创建中间模块启用真正的 ESM 命名导入:

```
// bridge/named.mjs
import named from '../commonjs/named.js'
export const sayHello = named.sayHello 
```

将其导入为命名导入

```
// index.mjs (with bridged modules)
// Named import without changing the name
import { sayHello } from './bridge/named.mjs'

// Named import changing the name
import { sayHello as say2 } from './bridge/named.mjs' 
```

## 将 ESM 模块导入 CJS 项目

您的依赖项现在在 ESM 中:

```
// esm/named.mjs
export function sayHello (name) { return `Hello ${name}` }

// esm/default.mjs
export default function sayHello (name) { return `Hello ${name}` } 
```

要从 CommonJS 文件中`require`它们，可以使用 [npm 包`esm`](https://www.npmjs.com/package/esm) 。这个“特殊”要求将所有内容作为命名导入的对象返回。ESM 默认导出成为返回对象
上名为`.default`的命名导入

```
const esmRequire = require('esm')(module)

// Named import without changing the name
const named = esmRequire('./esm/named.mjs')
const { sayHello } = named

// Named import changing the name
const { sayHello: say2 } = named

// "ESM default export" becomes a named import called "default"
const sayDefault = esmRequire('./esm/default.mjs').default

console.log(sayHello('World'))
console.log(say2('World'))
console.log(sayDefault('World')) 
```

如果你不想使用外部包，使用`import()`操作符。注意事项:

*   `import()`回报承诺。所以你需要`.then()`或者`await`
*   `import()`将所有内容作为命名导入的对象返回。要访问默认导出的东西，需要访问返回对象的属性`.default`

> ℹ️在此阅读更多关于`import()`

```
// index.js
;(async function () {
  // Named import without changing the name
  const named = await import('./esm/named.mjs')
  const { sayHello } = named

  // Named import changing the name
  const { sayHello: say2 } = named

  // Default import
  const sayDefault = (await import('./esm/default.mjs')).default

  console.log(sayHello('World'))
  console.log(say2('World'))
  console.log(sayDefault('World'))
})() 
```

### 替代:使用`esm`包制作中间模块

启用 CJS 默认导出:

```
// bridge2/default.js
require = require('esm')(module)
module.exports = require('../esm/default.mjs').default 
```

让其他库为 CJS 导入做好准备

```
// bridge2/named.js
require = require('esm')(module)
module.exports = require('../esm/named.mjs') 
```

并要求他们:

```
// Named import without changing the name
const named = require('./bridge2/named.mjs')
const { sayHello } = named

// Named import changing the name
const { sayHello: say2 } = named

// Default import
const sayDefault = require('./bridge2/default.mjs') 
```

* * *

# 就是这样！

下一篇文章将会是关于如何准备你的 Node.js 应用程序和库来尽快支持 es 模块！

# 进一步阅读

*   [*探索 JS*](https://exploringjs.com/impatient-js/ch_modules.html) 一书的“模块”章节，了解更多关于 CommonJS 模块和 es 模块之间差异的信息，如动态导出/导入
*   [ECMAScript 模块](https://nodejs.org/api/esm.html)，来自 Node.js 官方文档