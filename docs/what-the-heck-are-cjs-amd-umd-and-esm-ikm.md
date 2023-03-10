# Javascript 中的 CJS、AMD、UMD 和 ESM 到底是什么？

> 原文：<https://dev.to/iggredible/what-the-heck-are-cjs-amd-umd-and-esm-ikm>

一开始，Javascript 没有导入/导出模块的方法。这是一个问题。想象一下，只用一个文件编写你的应用程序，那将是一场噩梦！

然后，比我聪明得多的人试图给 Javascript 添加模块化。其中一些是 CJS、AMD、UMD 和 ESM。你可能听说过其中的一些(还有其他方法，但这些是大玩家)。

我将介绍高层次的信息:语法、目的和基本行为。我的目标是帮助读者在野外看到它们时认出它们💡。

# [t1 的特征](#cjs)

CJS 是 CommonJS 的缩写。下面是它的样子:

```
//importing 
const doSomething = require('./doSomething.js'); 

//exporting
module.exports = function doSomething(n) {
  // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

*   你们中的一些人可能会立即从 node 中认出 CJS 语法。这是因为节点[使用 CJS 模块格式](https://blog.risingstack.com/node-js-at-scale-module-system-commonjs-require/)。
*   CJS 同步导入模块。
*   您可以从库`node_modules`或本地目录导入。要么是由`const myLocalModule = require('./some/local/file.js')`要么是`var React = require('react');`的作品。
*   当 CJS 导入时，它会给你一个导入对象的**副本**。
*   CJS 不能在浏览器中工作。它必须被运输和打包。

# AMD

AMD 代表异步模块定义。下面是一个示例代码:

```
define(['dep1', 'dep2'], function (dep1, dep2) {
    //Define the module value by returning a value.
    return function () {};
}); 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
// "simplified CommonJS wrapping" https://requirejs.org/docs/whyamd.html
define(function (require) {
    var dep1 = require('dep1'),
        dep2 = require('dep2');
    return function () {};
}); 
```

Enter fullscreen mode Exit fullscreen mode

*   AMD 异步导入模块(因此得名)。
*   AMD[是为前端](http://tagneto.blogspot.com/2011/04/on-inventing-js-module-formats-and.html)做的(当它被提出的时候)(而 CJS 是后端)。
*   AMD 的语法不如 CJS 直观。我认为 AMD 是 CJS 的对立面。

# UMD

UMD 代表通用模块定义。下面是它的样子([来源](http://bob.yexley.net/umd-javascript-that-runs-anywhere/) ):

```
(function (root, factory) {
    if (typeof define === "function" && define.amd) {
        define(["jquery", "underscore"], factory);
    } else if (typeof exports === "object") {
        module.exports = factory(require("jquery"), require("underscore"));
    } else {
        root.Requester = factory(root.$, root._);
    }
}(this, function ($, _) {
    // this is where I defined my module implementation

    var Requester = { // ... };

    return Requester;
})); 
```

Enter fullscreen mode Exit fullscreen mode

*   工作于前端和后端(因此得名*通用*)。
*   与 CJS 或 AMD 不同，UMD 更像是一种配置多个模块系统的模式。查看[此处](https://github.com/umdjs/umd/)了解更多模式。
*   使用 Rollup/ Webpack 等捆绑器时，UMD 通常用作后备模块

# ESM

ESM 代表 ES 模块。实现一个[标准的](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)模块系统是 Javascript 的提议。我相信你们很多人都看过这个:

```
import React from 'react'; 
```

Enter fullscreen mode Exit fullscreen mode

其他野外目击:

```
import {foo, bar} from './myLib';

...

export default function() {
  // your Function
};
export const function1() {...};
export const function2() {...}; 
```

Enter fullscreen mode Exit fullscreen mode

*   适用于许多现代浏览器
*   它拥有两个世界的精华:CJS 式的简单语法和 AMD 的异步
*   [可摇树](https://developers.google.com/web/fundamentals/performance/optimizing-javascript/tree-shaking/)，由于 ES6 的[静态模块结构](https://exploringjs.com/es6/ch_modules.html#static-module-structure)
*   ESM 允许像 Rollup 这样的捆绑器[删除不必要的代码](https://dev.to/bennypowers/you-should-be-using-esm-kn3)，允许站点发送更少的代码以获得更快的加载。
*   可以在 HTML 中调用，只需做:

```
<script type="module">
  import {func1} from 'my-lib';

  func1();
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这可能不是在所有的浏览器中都 100%有效。

# 总结

*   ESM 是最好的模块格式，这要归功于它的简单语法、异步特性和树的可摇动性。
*   UMD 无处不在，通常被用作应急机制不起作用时的退路
*   CJS 是同步的，适合后端。
*   AMD 是异步的，适合前端。

感谢阅读，德夫斯！在未来，我计划深入地写每个模块，尤其是 ESM，因为它包含了许多令人惊叹的东西。敬请期待！

如果你注意到任何错误，请让我知道。

# 资源:

*   [基本 js 模块](https://www.freecodecamp.org/news/anatomy-of-js-module-systems-and-building-libraries-fadcd8dbd0e/)
*   [节点中的 CJS](https://blog.risingstack.com/node-js-at-scale-module-system-commonjs-require/)
*   [CJs-ESM 比较](https://jsmodules.io/cjs.html)
*   [发明 JS 模块格式和脚本加载器](http://tagneto.blogspot.com/2011/04/on-inventing-js-module-formats-and.html)
*   [为什么要用 AMD](https://requirejs.org/docs/whyamd.html)
*   [es6 模块浏览器兼容性](https://caniuse.com/#feat=es6-module)
*   [通过摇树减少 JS 有效载荷](https://developers.google.com/web/fundamentals/performance/optimizing-javascript/tree-shaking/)
*   [JS 模块-静态结构](https://exploringjs.com/es6/ch_modules.html#static-module-structure)
*   [浏览器中的 ESM](https://jakearchibald.com/2017/es-modules-in-browsers/)
*   [ES 模块深潜-卡通](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)
*   [使用 ESM 的理由](https://dev.to/bennypowers/you-should-be-using-esm-kn3)