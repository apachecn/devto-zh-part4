# 将 WASM 作为 ESM 加载到 NodeJS 中

> 原文：<https://dev.to/sendilkumarn/loading-wasm-as-esm-in-nodejs-2gdb>

# 什么是模块？

JavaScript 中的[模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)是一个逻辑名称空间，我们将在其中定义函数或/和值。然后我们可以导出这些函数或/和值，并将它们导入到其他一些名称空间中。

在 NodeJS 世界中，我们有 CommonJS 模块。

# 什么是 CommonJS？

为服务器和桌面创建了 CommonJS 模块。对于 CommonJS，导入和导出语法类似于下面这样:

```
// importing from a node_modules
const lodash = require('lodash');
// importing from another module
const localFunction = require('./some-module').someFunction;

// Exporting 
module.exports = {
    exportValue: someValue,
    exportFunction: someFunction
} 
```

在服务器中，(大部分)所有必需的 JavaScript 文件都位于文件系统中。这意味着它们可以同步加载。所以 CommonJS 模块系统是

*   小型的，紧凑的
*   允许同步加载
*   专为服务器打造

但是 CommonJS 没有*活绑定*。CommonJS 模块具有动态结构，这使得用绑定器进行静态检查、优化、消除死代码变得极其困难。

捆扎机或装载机生态系统做了一些智能黑客来实现这一点。

同样，在 CommonJS 中，很难识别和修复循环模块依赖关系。有时这可能会导致无限循环。

# ES 模块

另一方面，网络有一个高度不确定的网络。这带来了延迟，使工作变得困难。但是，网络仍然是最棒的事情。

曾多次尝试为 web 制作模块系统。但最终，ES2015 给了我们 [ESModules](https://www.ecma-international.org/ecma-262/6.0/#sec-modules) (或 ECMAScript 模块)。

```
// Importing a node modules
import lodash from 'lodash';
// Importing a function from another ESModule
import {someFunction} from './some-module';

// Exporting values or functions
export const exportValue = someValue;
export function exportFunction = someFunction; 
```

ESModules 是为 web 构建的。也就是说，它们支持**异步**加载。

同样重要的是，要有清晰、简洁和紧凑的语句，便于编写人员和装卸工或打包工理解。

ESModules 是

*   更紧凑
*   异步加载
*   专为网络打造
*   有效管理循环模块依赖关系
*   静态结构使得检查、优化、消除死代码变得容易

ECMAScript 模块正在 NodeJS 生态系统中慢慢稳定下来。这确实花了一段时间，但都是好的。我们在 NodeJS 中计划和交付 ESModules。点击查看更多详情[。](https://github.com/nodejs/modules/blob/master/doc/plan-for-new-modules-implementation.md)

目前，我们为节点和浏览器环境编写的 JavaScript 是不同的。这对库作者、开发者和其他人来说很困难。让 JavaScript 在节点和浏览器之间同构会很牛逼。它将减少大量的样板代码。

将 ESModules 引入 NodeJS 让我们在节点和浏览器之间架起了一座桥梁。

桥越窄，生态系统就越好。

# ESModules 来到 Node.js

在浏览器中，我们使用`type="module"`来区分脚本标签中的 ESModules。同样，在 NodeJS 世界中，我们将使用`.mjs`扩展来区分 ESModules。

我们可以使用`import`语法导入`.mjs`文件。

```
// add.mjs
export let methodCalled = 0;

export function add(x, y) {
    methodCalled++;
    return x+y;
} 
```

```
// main.mjs
import assert from 'assert';
import {methodCalled, add} from './add.mjs';

assert(methodCalled, 0); 
assert(add(13, 31), 44);
assert(methodCalled, 1); 
```

我们可以使用`node --experimental-modules main.mjs`编译并运行上面的代码。

`experimental-modules`标志指定将`main.mjs`文件作为 ESModule 加载的节点。

### 无默认分辨率

目前，模块实现不能解析为`index`文件或添加扩展名`.mjs`。那就是

```
import {add} from './add'; // is not a valid ESM import
                          // nor it looks for index file in add folder 
```

### 中没有混合语法。mjs 文件

使用当前的实现，您不能混合和匹配语法。那就是`.mjs`文件应该只使用`import`语句来导入。

```
const assert = require('assert');
             ^
ReferenceError: require is not defined 
```

### 在 mjs 文件中加载 js 文件

ESModules JavaScript 文件(`.mjs`)可以导入 CommonJS 文件(`.js`)。

为了导入`.js`，我们必须使用 [createRequire](https://nodejs.org/api/modules.html#modules_module_createrequire_filename) 函数。

```
// add.js

let methodCalled = 0;

function add(x, y) {
    methodCalled++;
    return x+y;
}

module.exports = {
     methodCalled,
     add
} 
```

在`.mjs`文件中，让我们导入`add.js`文件。

```
//main.mjs

import { createRequire } from 'module';      // ---1
const require = createRequire(import.meta.url); // ---2

const { add } = require('./add.js'); // ---3

console.log(add(13, 10)); // 23 
```

1.  我们从`module`导入`createRequire`。[NodeJS 代码](https://nodejs.org/api/modules.html#modules_the_module_object_1)中有`module`。

2.  然后我们定义`require`。所需用途`import.meta.url`。点击查看更多关于`import.meta`T3 的信息

3.  使用`require`功能加载库。

然后我们可以使用 add 函数，就像任何其他导入的函数一样。

### 在 js 文件中加载 mjs 文件

这是不可能的。

# es modules 是如何工作的？

这里有一个来自林·克拉克的非常棒的博客。

ESModules 加载有三个阶段:
1 .获取并解析
2。链接
3。评价

#### 获取&解析

顾名思义，在这个阶段，提到的 JavaScript 文件是从任何给定的 URL 获取的。URL 可以是远程位置(通常在浏览器中)或绝对文件 URL。获取文件后，将对其进行解析。

在解析过程中，依赖关系(或模块)被逐步识别。然后它获取所有的模块并解析它们。解析确保 JavaScript 具有有效的语法。

这个阶段以创建一个`Module record`结束。将模块记录视为一个实例，它保存模块中定义的所有内容。像进口、出口和其他的事情。

#### 链接阶段

在这个阶段，使用模块记录映射导出和导入的链接。链接只是将值链接到一个位置，而不是一个值。这为导入启用了动态绑定。

因此导入的值将总是反映实时值。

#### 评估

在这个阶段，

*   模块的词法范围被初始化
*   函数被托管
*   函数声明被初始化，JavaScript 代码被求值，值被填入内存位置。

# 进入 WebAssembly 模块

WebAssembly 是这个街区最酷的新成员。它为浏览器带来了可维护的性能和本机代码。

# WASM 的 ESM

目前，WebAssembly 模块的 [ESModules 集成处于第 1 阶段。](https://github.com/WebAssembly/esm-integration/tree/master/proposals/esm-integration)

让我们来看看将 WebAssembly 模块作为 ES 模块加载与通过 JavaScript 加载的主要区别。

ESModules 加载有三个阶段(类似于 JavaScript):

*   获取并解析
    *   对二进制格式进行解析和验证。
*   环
    *   这里没有函数初始化
*   评价
    *   初始化模块
    *   运行启动功能
    *   初始化函数声明

# 在 NodeJS 中加载 WASM 作为 ESM

让我们首先创建一个 WebAssembly 模块。生成 WebAssembly 模块最简单、最方便的方法是使用 WebAssembly 文本格式。

创建一个名为`add.wat`的文件，包含以下内容

```
(module
  (func $add (param $p1 i32) (param $p2 i32) (result i32)
    local.get $p1
    local.get $p2
    i32.add)
  (export "add" (func $add))
) 
```

使用 [wabt](https://github.com/WebAssembly/wabt)
将上述 WebAssembly 文本格式转换成 WebAssembly 模块

```
/path/to/wabt/build/wat2wasm add.wat -o add.wasm 
```

它创建了`add.wasm`

```
00 61 73 6d 01 00 00 00 01 07 01 60 02 7f 7f 01
7f 03 02 01 00 07 07 01 03 61 64 64 00 00 0a 09
01 07 00 20 00 20 01 6a 0b 
```

现在我们可以像导入 ESModule 一样导入 WebAssembly 模块。让我们用以下内容创建一个名为`index.mjs`的文件。

```
import * as M from './add.wasm';

console.log(M.add(10, 13)); // 23 
```

我们可以使用两个标志运行上面的代码，一个用于启用
`experimental-modules`和`experimental-wasm-modules`。

```
node --experimental-modules --experimental-wasm-modules index.mjs 
```

检查[提交](https://github.com/nodejs/node/commit/bbc254db5db672643aad89a436a4938412a5704e)在节点核心中的位置。

参考链接，进一步了解`--experimental-modules`

[NodeJS 公告](https://medium.com/@nodejs/announcing-a-new-experimental-modules-1be8d2d6c2ff)

我希望这能让您在 ESModules 方面有一个良好的开端。如果您有任何问题/建议/觉得我错过了什么，请随时添加评论。

如果你喜欢这篇文章，请留下赞或评论。

你可以在 [Twitter](https://twitter.com/sendilkumarn) 和 [LinkedIn](https://linkedin.com/in/sendilkumarn) 上关注我。

感谢 [@MylesBorins](https://twitter.com/MylesBorins) 的牛逼评论。:)