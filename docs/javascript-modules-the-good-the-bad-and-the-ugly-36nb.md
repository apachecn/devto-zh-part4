# JavaScript 模块，好的，坏的和丑陋的🧐

> 原文：<https://dev.to/yashints/javascript-modules-the-good-the-bad-and-the-ugly-36nb>

如果你偶然发现了一段普通的 JavaScript 代码，想把它重构为一个模块，或者有一个`CommonJS`模块，想把它转换为 [`ES6 Modules`](https://tc39.es/ecma262/#sec-modules) ，你可能会遇到一些棘手的情况。我最近不得不经历其中的一个，偶然发现了一些你在使用模块时需要注意的不同点。和往常一样，我认为分享这些会帮助其他人，所以我们开始吧😊。

## 常见& `require`

在 ES6 被上帝放在地球上之前，这是许多开源项目中最常见的代码类型😁。

### 用法

```
const _ = require('underscore'); // from an npm package 

const reverseString = require('./reverseString.js'); // from internal module 
```

Enter fullscreen mode Exit fullscreen mode

### 定义

在`reverseString.js`中，你需要写一些类似这样的东西来让它工作:

```
const reverseString = (sentence) => sentence.split("").reverse().join("");

module.exports = reverseString; 
```

Enter fullscreen mode Exit fullscreen mode

你唯一需要注意的是，你赋给`module.exports`的值和你使用`require`时得到的值是一样的。而如果要使用刚刚导出的函数:

```
const reverseString = require('./reverseString.js');

console.log(reverseString("madam")); // madam, gotcha 😂 
```

Enter fullscreen mode Exit fullscreen mode

### 多出口

在现实生活中，我们需要从模块中导出不止一个函数。这就像将所有这些都包装在一个对象中一样简单。假设您有一个名为`stringHelpers.js` :
的文件

```
const reverseString = (sentence) => {...};
const toUpperCase = (sentence) => {...};
const convertToCamelCase = (sentence) => {...};

module.exports = {
  reverseString: reverseString, 
  toUpperCase, // you can omit the assignment if the name is equal
  toLowerCase: convertToLowerCase,
}; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`module.exports`的值将是一个对象，这意味着当使用它时，您将不得不使用对象上的属性:

```
const stringHelpers = require('./stringHelpers.js');

console.log(stringHelpers.reverseString('racecar')); // racecar 🤣 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以用不同的方式重写我们的模块:

```
module.exports = {};

module.exports.reverseString = () => {...};
module.exports.toUpperCase = () => {...};
module.exports.toLowerCase = () => {...}; 
```

Enter fullscreen mode Exit fullscreen mode

这两种创建模块的方式是完全一样的，这取决于你遵循哪种约定。

## ES6 模块

[ES6 模块](https://tc39.es/ecma262/#sec-modules)的创建是为了创建一种`CommonJS`和`AMD`(异步模块定义)用户都满意的格式。与`CommonJS`方法相比，ES6 模块 ***最简单的形式总是*** 导出一个对象。

```
const reverseString = (sentence) => {...};

export default reverseString; 
```

Enter fullscreen mode Exit fullscreen mode

### 默认导出

拥有模块的主要好处之一是隐藏内部实现细节，只暴露需要的部分。在这种情况下，我只导出一个函数，此外，我还将它作为`default`导出。当您将某个东西导出为`default`时，您可以使用它们的原始名称甚至别名来导入它。另外你可以省略花括号。

```
import reverseString from './reverseString.js';

import defaultExport from './reverseString.js';

console.log(reverseString('madam')); //madam
console.log(defaultExport('madam')); //madam 
```

Enter fullscreen mode Exit fullscreen mode

[![Made up name gif](img/8831efae6a12fc1b07b2f43c50e8296b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tii4aeTF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x7xz4286i3myxoad6y0p.gif)

如果你查看从文件中导出的对象，你会看到下面的对象:

```
{
  default: (sentence) => {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

也可以直接导出函数:

```
export const reverseString = (sentence) => {...}; 
```

Enter fullscreen mode Exit fullscreen mode

这将导致:

```
{
  reverseString: (sentence) => {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，您将需要使用它的名称才能导入它，而且您必须使用花括号:

```
import { reverseString } from './reverseString.js';

console.log(reverseString('madam')); //madam 
```

Enter fullscreen mode Exit fullscreen mode

### 混合导出

除了命名导出之外，您还可以有一个默认导出:

```
export const reverseString = (sentence) => {...};
export const toUpperCase = (sentence) => {...};
const convertToCamelCase = (sentence) => {...};

export default convertToCamelCase; 
```

Enter fullscreen mode Exit fullscreen mode

然后它会给你:

```
{
  reverseString: (sentence) => {...},
  toUpperCase: (sentence) => {...},
  default: (sentence) => {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

并且在导入时，可以使用它们的名称，或者导入一个对象中的所有内容:

```
import convertToCamelCase, { reverseString, toUpperCase } from './stringHelpers.js';

// or

import * as stringHelpers from './stringHelpers.js'; 
```

Enter fullscreen mode Exit fullscreen mode

公平地说，您也可以在导出后更改命名导出的名称:

```
import { reverseString as madeUpName } from './stringHelpers.js'; 
```

Enter fullscreen mode Exit fullscreen mode

### 导入整个模块

有时你有一个代码块需要在不需要访问任何模块内部值的情况下执行。在这种情况下，您可以导入整个模块来执行它的全局代码:

```
// other code or possible exports

window.addEventListener("load", function() {
    console.log("Window is loaded");
}); 
```

Enter fullscreen mode Exit fullscreen mode

然后导入整个模块:

```
import './loadEventListener.js'; 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么要使用模块😍？

使用 ES6 模块(甚至是 CommonJS 格式)有很多好处。我将在这里介绍其中的一些:

*   易于共享代码(内部和项目之间)。
*   独立可测性。
*   有能力隐藏实现细节。
*   单一责任原则，代码可以被分割成具有特定用途的小块。
*   简化依赖性检测/注入。
*   为代码块定义清晰的接口。
*   可以与依赖注入系统一起使用来加载代码块。
*   可以帮助树抖消除不用的代码。

## 有什么蹊跷🤔？

使用 ES6 模块时，有几点需要注意:

*   它们默认运行在严格模式下(你不再需要有`use strict`)。
*   `this`的顶层值为`undefined`。
*   顶层变量是模块的局部变量。
*   ES6 模块是异步加载和执行的。这意味着浏览器将首先完成对`HTML`的解析和加载，然后执行模块代码。可以并行加载，也可以使用`link rel=preload`预先加载。

## 不给糖就捣蛋？

这可能是我最喜欢的部分。您可以动态加载模块并执行它。这是通过使用`import`关键字作为函数而不是普通命令来完成的。

```
import('/modules/my-module.js')
  .then((module) => {
    // Do something with the module.
  }); 
```

Enter fullscreen mode Exit fullscreen mode

或者更好:

```
const module = await import('/modules/my-module.js'); 
```

Enter fullscreen mode Exit fullscreen mode

### 哇太好了，但为什么是🧐？

假设您有一个应用程序，它在移动和桌面上有不同的用户体验或行为。仅仅有一个响应式的设计是无法解决这个问题的，所以你构建了一个*页面呈现器*，它根据访问者的平台加载并呈现每个页面。

从技术上讲，这只是一种策略模式，页面呈现器决定在运行时加载哪个模块。使用动态导入可以很容易地解决这个问题。还有许多其他用例可以从动态导入中受益。

然而，权力越大，责任越大。你需要小心使用这个神奇的功能，因为它有自己的缺点。至少，您会失去自动绑定延迟加载的块、类型推断等功能。

## 如何使用它们？

在本文中，我已经向您展示了许多如何使用另一个文件或模块中的模块的例子。然而，有时你需要在浏览器中使用它们(从`HTML`)。Chrome、Safari、Firefox 和 Edge 都支持 ES6 模块，但是你需要将你的脚本标签的类型从 script 改为 module:

```
// html.js
export function tag (tag, text) {
  const el = document.createElement(tag)
  el.textContent = text

  return el
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<script type="module">
  import { tag } from './html.js'

  const h1 = tag('h1', ' Hello Modules!')
  document.body.appendChild(h1)
</script> 
```

Enter fullscreen mode Exit fullscreen mode

或者只是简单地将模块文件导入另一个文件并使用外部引用:

```
// app.js
import { tag } from './html.js'

const h1 = tag('h1', ' Hello Modules!')
document.body.appendChild(h1) 
```

Enter fullscreen mode Exit fullscreen mode

```
<script type="module" src="app.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

**提示**:现在还有一些旧浏览器**咳** IE11 **咳**不支持它，所以确保你有一个备份计划。这可以使用`nomodule`属性来完成。

```
<script type="module" src="module.js"></script> <script nomodule src="fallback.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

我们看了一下 ES6 模块之前和之后，看到了语法上的一些差异。我们看到了 JavaScript 中模块系统的强大之处，以及在更大的代码库中使用它的好处。最后我们回顾了具有巨大潜力但应谨慎使用的动态导入。

希望你喜欢这本书，下次再见👋🏼。