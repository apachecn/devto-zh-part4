# 实用指南:Node.js 12 支持 ES 模块。你的应用准备好了吗？

> 原文：<https://dev.to/exacs/practical-guide-node-js-12-supports-es-modules-are-your-apps-ready-5gi>

***2020 年 5 月更新**。到 2020 年 10 月，Node.js 14 将会发布。自从我写这篇文章以来，有些事情已经改变了(2019 年 7 月)*

如果您是 Node.js 开发人员，编写 Node.js 应用程序或库，您可能知道 Node.js 12 支持 ECMAScript 标准模块！

*   要在 Node.js 12 中启用 ESM，仍然需要`--experimental-modules`标志。阅读更多[官方文件](https://nodejs.org/docs/latest-v12.x/api/esm.html#esm_enabling)
*   node . js14**可能**会发布支持 ESM 的无标志版本。阅读更多关于[官方文件](https://nodejs.org/docs/latest-v14.x/api/esm.html)

* * *

问题是…你的应用和库准备好了吗？

这篇文章探索了在不使用任何构建过程的情况下为 Node.js 12 *编写模块化应用和库的方法。写*的代码*正是执行*的代码*。*

⚠️ **小心！**这个帖子是用 2020 年 5 月的“共识”和 Node.js 的*当前*行为(Node.js 12 有`--experimental-modules`标志，Node.js 14 没有)写的。很有可能，但不能保证 14 号在 10 月到达 LTS 后会有同样的表现。只要有可能，提及官方📝将包括 Node.js 文档，以便您可以阅读它们并做出自己的决定。

> 🧪目前处于试验阶段，但一旦 Node.js 达到 LTS，很可能会保持原样的东西都标有这个符号。几乎从第一天起就阅读它们，成为 ES 模块的早期采用者，但是在 LTS 发布之前不要在生产中使用它。
> 
> 💥标有这个符号的事物会引起突破性的变化。这对库作者来说尤其重要。在 LTS 发布之前，不应在生产中使用它们。
> 
> 🛑标有这个符号的东西做起来更危险，即使对早期使用者来说也是如此，因为它们可能会在未来发生变化。

# 过渡阶段。计划一下！

理想情况下 Node.js 项目会逐渐采用 ES 模块，停止使用 CommonJS。这在 Node.js 12 中是可能的，它取决于开发人员的采用等级(以及您的外部依赖)。

在 Node.js 12 和 14 中，CommonJS 仍然是默认选项，但 ES 模块将是未来版本中的首选。

花点时间来计划过渡。否则，您可能会同时维护同一个项目的两个或三个版本。

你能做什么？

## 总结

这是一个 4 级过渡路线图示例:

|  | 没有收养 | 软采用 | 艰难收养 | 完全收养 |
| --- | --- | --- | --- | --- |
|  |  | 🧪 | 💥 | 🛑 |
| CommonJS | 系统默认值 | 系统默认值 | 反对 | 减少 |
| ES 模块 | 有缺陷的 | 使能够 | 系统默认值 | 系统默认值 |

无采用和完全采用级别等同于只使用 CommonJS 或只使用 ES 模块，不需要更多的解释。

让我们深入了解软和硬采用级别

## 软收养。启用 ES 模块，保持 CommonJS 为默认

目标:

*   使用 ESM 语法编写自己的代码。
*   提供 ESM API。
*   开始贬低 CJS API。
*   不要做任何破坏性的改变。
*   准备放弃 CJS。

因为这都是关于依赖关系的，万一有些文件在 ESM，有些在 CJS，你将面临:

1.  `index.js`(迁移到 ESM)依赖于`module.js`(在 CJS)
2.  `index.js`(在 CJS)依赖于`module.js`(迁移到 ESM)

### `index.js`(迁移到 ESM)依赖`module.js`(在 CJS)

在这种情况下，我们首先迁移`index.js`保持 CommonJS 中的依赖关系。

将`index.js`重命名为`index.mjs`以在该文件上启用 ESM 并禁用 CJS。现在，`require`在这个文件中已经不起作用了。

如果您的一些依赖项仍然在 CommonJS 中，请确保理解这些风格之间的互操作性。

### `index.js`(在 CJS)依赖`module.js`(迁移到 ESM)

> ❓🧪根据你对`esm` npm 包的信任或对`import()`操作员的信心来使用它

确保您的依赖项具有`.esm`扩展名。相应地使用命名导出和默认导出。

您需要理解这些风格之间的互操作性。尤其需要了解 [npm 包`esm`](https://www.npmjs.com/package/esm) 或者 [`import()`运算符](https://2ality.com/2017/01/import-operator.html)

### 担任本库作者

> 🧪这些变化是不会中断的。然而，由于 ES 模块仍处于试验阶段，建议您执行 **preminor** 或 **prepatch** 版本或任何其他“测试版”升级:运行`npm version preminor`、`npm version prepatch`或类似版本。发布一个稳定版本的库来强迫用户使用实验性的特性并不是一个好主意。

作为库作者的目标:

*   提供两个入口点:CommonJS 模块和 ES 模块。
*   将 CommonJS 入口点作为主要入口点。
*   推荐`mjs`方案。🧪

如果您的整个库被迁移，您的 ESM 条目文件将类似于`index.mjs` :

```
// my-lib/index.mjs
export default function sayHello (name) {
  return `Hello from my-lib/esm: ${name}`
} 
```

创建一个`index.cjs`。一旦你改变你的库默认支持`esm`，这将是遗留代码
的入口点

```
// my-lib/index.cjs
require = require('esm')(module)
module.exports = require('./index.mjs') 
```

将`index.mjs`和`index.cjs`作为您的 npm 包的一部分交付(编辑您的 package.json)。将`index.cjs`作为“主”入口

```
{
  "main": "index.cjs",
  "files": [
    ...,
    "index.mjs",
    "index.cjs"
  ],
} 
```

> 📝Node.js 可以在`package.json`中采用一个字段名称，专门用于 ES 模块。在这种情况下，您可以同时包含“名称”和新字段。[关注讨论](https://github.com/nodejs/modules/issues/273)

在这个阶段，如果库用户想要通过显式路径使用 ESM 版本，他们应该**显式**访问`index.mjs`。

```
import sayLibrary from 'my-lib/index.mjs'
console.log(sayLibrary('World')) 
```

> 📝在 [Node.js 帮助](https://nodejs.org/api/esm.html#esm_package_entry_points)中阅读更多信息

#### 另类。将`index.js`文件(主条目)保存为`index.cjs`的废弃副本

提供三个切入点，保持`index.js`为主要切入点。

```
{
  "main": "index.js",
  "files": [
    "index.js",
    "index.mjs",
    "index.cjs"
  ],
} 
```

为了鼓励 CommonJS 用户明确要求使用`index.cjs`文件，不推荐使用`index.js`。

```
// my-lib/index.js
require = require('esm')(module)
process.emitWarning('This library will be a ES module in the next major version. If you still need to use the CommonJS version, require("my-library/index.cjs") instead')
module.exports = require('../index.mjs') 
```

本库也可以推荐使用 ES 模块版本。

> 🧪从一个稳定版本中“推荐”一个实验性的解决方案并不是一个好主意。记住发布你的库作为一个不稳定的版本(比如预发布，alpha 等。)

库是必需的/导入的，如下所示:

|  | 以前 | 现在 | 下一版本 |
| --- | --- | --- | --- |
| `require(my-lib)` | 好 | **已弃用** | 减少 |
| `require(my-lib/index.cjs)` | 不适用的 | **遗产** | 反对 |
| `import my-lib/index.mjs` | 不适用的 | **🧪没事** | 好 |
| `import my-lib` | 不适用的 | **不适用** | 被推荐的 |

#### 多余。包含多个文件的库

如果你像这样从你的库中提供多个端点:

```
const f1 = require('my-library/function1')
const f2 = require('my-library/function2') 
```

📝您可能希望阅读[包装出口提案(仍处于“PR 前”状态)](https://github.com/jkrems/proposal-pkg-exports)

### 本阶段的附加说明

*   新代码应该直接用 ES 模块编写。
*   如果您正在编写一个库，请确保您使用了正确的标准
*   如果你正在编写一个库，确保提供一个 CommonJS 的替代品，并使它成为默认的**库**，即使你想弃用它。确保对照`Node.js < 12`进行测试

## 过渡 2。默认启用 ESM。支持常见问题

> 在没有实验标志的情况下支持 ESM 之前，🛑不会在生产中这样做。

目标:

*   默认情况下使用 ES 模块
*   仍然支持传统的共性，但反对它
*   删除不推荐使用的代码

### 做出突破性的改变！

在项目的`package.json`文件中添加:

```
{
  "type": "module"
} 
```

现在所有的`.js`文件都被当作`.mjs`了。在任何`.js`或`.mjs`文件中不能再使用`require`。

`.js`和`.mjs`扩展可以无缝地工作。

> 你可以用两者来区分脚本和模块…但这只是一种观点。例如，您可以保留一个`index.js`文件，但使用`.mjs`作为其依赖项的扩展名。

将**所有**仍使用 CommonJS 的`.js`文件重命名为`.cjs`。修复导入/导出路径

号外。弃用剩余的`.cjs`文件(您应该很快会丢弃它们！).

### 库作者。做出突破性的改变！

> 💥本节中的每一个步骤都会引起突破性的变化。如果你是一个库作者，你可能想做一个包含所有变化的**单一主版本**
> 
> 🧪在这一节中的每一步也处于实验状态。
> 
> 建议:做一个大+不稳定的发布:`npm version premajor`。

⚠️⚠️⚠️在你的项目的`package.json`文件中添加“类型”(💥将“类型”改为“模块”也是一个突破性的改变)并指向`index.mjs`文件作为库的入口:

```
{
  "type": "module",
  "main": "index.mjs" ⚠️⚠️⚠️
} 
```

⚠️⚠️⚠️:不能保证这个油田会被称为“主油田”。

> 📝实际上[官方公告](https://github.com/nodejs/modules/blob/master/doc/announcement.md#es-module-code-in-packages)说:
> 
> 虽然我们知道社区已经接受了“模块”字段，但 Node.js 不太可能采用该字段[…]在此问题解决之前，请不要发布任何旨在供 Node.js 使用的 ES 模块包。
> 
> 📝然而,[路线图文档](https://github.com/nodejs/modules/blob/master/doc/plan-for-new-modules-implementation.md)说:
> 
> 现状是当前的`--experimental-modules`行为:`"main"`恰好指向一个文件，所有的文件扩展名都是强制的(默认情况下)，所以在 ESM 和 CommonJS 中不可能有`import`说明符指向不同的文件。
> 
> 现状已达成共识，在没有任何其他提案达成共识的情况下，现状将继续发展。“新领域”提议缺乏共识。

将“类型”设置为“模块”后，将裸路径与 CommonJS ( `require(my-lib)`)一起使用不再有效(💥改变访问 API 的方式是一个突破性的改变)

一旦`index.mjs`和`index.cjs`都可以访问，您就可以删除`index.js`文件。此外，如果您打算放弃 CommonJS 支持，可以在`index.cjs`中添加一个反对警告。

```
require = require('esm')(module)
process.emitWarning('CommonJS support will end in the next major version of this library')
module.exports = require('../index.mjs') 
```

您还可以将`esm`(我们仅用于遗留支持的库)标记为您的库的“可选依赖”。使用 es 模块的用户不需要安装库。💥**将依赖转换为可选总是一个突破性的变化**

|  | 现在 | 然后 |
| --- | --- | --- |
| `require(my-lib)` | **放弃了💥** | 减少 |
| `require(my-lib/index.cjs)` | **已弃用** | 减少 |
| `import my-lib/index.mjs` | **好的** 🧪 | 好 |
| `import my-lib` | **好的** 🧪 | 好 |

* * *

# 就是这样！

在未来的帖子中，我可能会提到一些关于创作其他类型的库的内容:用于/用 TypeScript 编写的库，用于前端 javascript 的库，同构库…谁知道呢！

我还想讨论一下采用 es 模块的后果:从文本编辑器中完成代码，遵循标准，等等。

# 进一步阅读:

*   [*探索 JS*](https://exploringjs.com/impatient-js/ch_modules.html) 一书的“模块”章节，了解更多关于 CommonJS 模块和 es 模块之间的差异，如动态导出/导入
*   [ECMAScript 模块](https://nodejs.org/api/esm.html)，来自 Node.js 官方文档
*   [这篇博客文章](https://2ality.com/2019/04/nodejs-esm-impl.html)来自 2ality 博客。
*   [node . js 官方公告](https://medium.com/@nodejs/announcing-a-new-experimental-modules-1be8d2d6c2ff)
*   [新模块实施计划](https://github.com/nodejs/modules/blob/master/doc/plan-for-new-modules-implementation.md)