# 用 TypeScript 编写代码分析器(从头开始)

> 原文：<https://dev.to/xpbytes/writing-a-code-analyzer-in-typescript-5ec3>

exercisem 是一个在线平台，旨在通过实践和指导帮助您提高编码技能。

[exercisem](https://exercism.io)为您提供数千种不同语言的练习。一旦你开始学习一门语言，你就会看到一系列需要完成的核心练习。每一个都是有趣的挑战，旨在教你更多关于语言的特性。

在写这篇文章的时候，我是 [JavaScript](https://exercism.io/tracks/javascript) 和 [TypeScript](https://exercism.io/tracks/typescript) track 和[最近](https://exercism.io/blog/track-anatomy-project)的维护者，我们一直致力于自动化体验的一部分。

> 本文将向您介绍使用与 **ESTree** 兼容的工具进行 AST 解析和遍历。它特别关注某些令牌类型，最常见于 *JavaScript* 和 *TypeScript* 代码中。
> 
> 它教你如何**亲自探索**这些树，并参考**代码样本**和实际的产品实现。

阅读本文时，请考虑您自己的 JavaScript 和 TypeScript 代码。一旦你理解了浏览器(和工具，如 [`eslint`](https://github.com/eslint/eslint) )如何解析你的代码，你可能会更好地理解语言是如何定义和构造的。

> 🚧到 *GitHub* 的链接都是`master`链接，这意味着在你写这篇文章和点击它们之间，内容可能会改变。然而，为了确保代码样本有意义，分析器存储库链接引用了一个特定的提交(`9ff332b`)。这意味着您看到的代码可能与今天使用的不同。

## 目录

*   [目录](#table-of-contents)
*   [📝演习](#the-exercise)
    *   [JavaScript 实现](#javascript-implementation)
    *   [打字稿实现](#typescript-implementation)
*   [💯最优解](#optimal-solutions)
    *   [JavaScript 解决方案](#javascript-solution)
    *   [打字稿解决方案](#typescript-solution)
*   [👩🏽‍💻分析代码](#analysing-the-code)
    *   [💬抽象语法树](#abstract-syntax-trees)
    *   [🏃🏽‍💨运行解析器](#running-the-parser)
    *   [🔎寻找主入口点](#finding-the-main-entrypoint)
    *   [🔎寻找顶级常数](#finding-the-top-level-constant)
*   [算法](#the-algorithm)
    *   [物体的属性](#properties-of-objects)
    *   [“执行”一个属性](#executing-a-property)
    *   [匹配标识符](#matching-the-identifiers)
*   [✅自动化指导](#automated-mentoring)
    *   [📦测试出口](#testing-exports)
    *   [内联出口](#inline-exports)
    *   [说明符导出](#specifier-exports)
    *   [CommonJS 导出](#commonjs-exports)
*   [🔀测试变量](#testing-varations)
*   [行走的打字树](#walking-typescript-trees)
    *   [🔑访客钥匙](#visitor-keys)
    *   [📖类型注释](#type-annotations)
    *   [类属性](#class-properties)
    *   [↩返回类型](#return-types)
*   [结论](#conclusion)
*   [参考](#reference)
    *   [分析仪参考值](#analyzer-reference)
    *   [练习库](#exercism-repositories)
    *   [套餐](#packages)

[![Photo called 'Feel the freedom' in Dungeness, United Kingdom, displaying a red Volkswagen Samba parked near brown house.](img/527008cbedfc8848679a0e43fc5a7bd4.png "Photo by Zoltan Tasi (https://unsplash.com/@zoltantasi) on Unsplash (https://unsplash.com/)")](https://res.cloudinary.com/practicaldev/image/fetch/s--8g5OZ0Aj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bq292ll5t7s7m0o0rtwd.jpg)

## 📝练习

在这篇文章中，我将为 [`gigasecond`](https://github.com/exercism/problem-specifications/blob/master/exercises/gigasecond) 练习编写分析器，用于类型脚本和 JavaScript 轨道。描述只有两行:

> 给定一个时刻，确定十亿分之一秒后的时刻。
> 
> 一千兆秒是`10^9`(10 亿，000 万)秒。

[规范数据](https://github.com/exercism/problem-specifications/blob/master/exercises/gigasecond/canonical-data.json)暗示了我需要编写的代码，但幸运的是，这个练习在 [JavaScript](https://github.com/exercism/javascript/blob/master/exercises/gigasecond/gigasecond.spec.js) 和 [TypeScript](https://github.com/exercism/typescript/blob/master/exercises/gigasecond/gigasecond.test.ts) 轨道中都实现了。

### JavaScript 实现

JavaScript 实现希望我们编写一个命名的导出`gigasecond`，它返回一个比输入`Date`晚十亿秒的`Date`。

```
 // Test case from the JavaScript track
  test('tells a gigasecond anniversary since midnight', () => {
    const gs = gigasecond(new Date(Date.UTC(2015, 8, 14)));
    const expectedDate = new Date(Date.UTC(2047, 4, 23, 1, 46, 40));
    expect(gs).toEqual(expectedDate);
  }); 
```

### TypeScript 实现

TypeScript 实现希望我们编写一个默认导出`Gigasecond`，它是一个具有`date()`函数的类，该函数返回一个超过构造函数`Date`十亿秒的`Date`。

```
 // Test case from the TypeScript track
  it('tells a gigasecond anniversary since midnight', () => {
    const gs = new Gigasecond(new Date(Date.UTC(2015, 8, 14)))
    const expectedDate = new Date(Date.UTC(2047, 4, 23, 1, 46, 40))
    expect(gs.date()).toEqual(expectedDate)
  }) 
```

## 💯最佳解决方案

在讨论如何为这两种实现编写分析器之前，我首先必须确定最佳解决方案是什么。如果我知道预期的代码结果，我可以试着识别它并从那里开始工作。

### JavaScript 方案

JavaScript 的实现非常简单。它使用 [`Date`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) 构造函数连同 [`Date#getTime`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getTime) 和一个常数来生成一个适当的结果。

```
const GIGASECOND_IN_MS = (10 ** 9) * 1000

export function gigasecond(date) {
  return new Date(date.getTime() + GIGASECOND_IN_MS)
} 
```

注意这里的特殊性是至关重要的:

*   最佳方法是提取`GIGASECOND_IN_MS`值作为顶级常量
*   常量的值(`(10 ** 9) * 1000`)可以用许多同样有效的形式写出来。然而，把数字写出来被认为是一种气味。以下所有**应被认为是最佳的**:
    *   `10 ** 12`
    *   `1e9 * 1e3`
    *   `1e12`，
    *   `Math.pow(10, 9) * 1000`
    *   `Math.pow(10, 12)`
*   [`Date#valueOf`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/valueof) 是*不是*优。它被标记为“该方法通常由 JavaScript 在内部调用，而不是在代码中显式调用。”，即使它在功能上是等价的。
*   最后， [`Date.parse(date)`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/parse) 不是一个好的候选，因为它应该只与字符串一起工作。当给定一个日期时，它返回与`getTime`相同的值，这是因为该日期被强制转换为一个字符串，然后被解析。

### 打字稿解

TypeScript 实现期望一个`class`作为`default export`，并且有一个方法`date()`。该算法与 JavaScript 解决方案中的算法*完全*相同，但是它需要类型注释。

```
const GIGASECOND_IN_MS = (10 ** 9) * 1000

export default class Gigasecond {

  private readonly futureDate: Readonly<Date>

  constructor(date: Readonly<Date>) {
    this.futureDate = Object.freeze(new Date(date.getTime() + GIGASECOND_IN_MS))
  }

  date(): Readonly<Date> {
    return this.futureDate
  }
} 
```

除了前面为 JavaScript 描述的变化和规则，计算可以在*中完成，或者在`constructor`(如上所示)*中的*中完成，或者在`date`函数中的*中完成。在最后一种情况下，它将如下所示:

```
const GIGASECOND_IN_MS = (10 ** 9) * 1000

export default class Gigasecond {

  constructor(private readonly date: Date) { }

  date(): Date {
    return new Date(date.getTime() + GIGASECOND_IN_MS)
  }
} 
```

## 👩🏽‍💻分析代码

现在是实际编写分析器的时候了。我们将首先关注 JavaScript 实现。因为已经有 JavaScript 分析器在野外运行，并且这些工作是开源的，所以这个例子将使用来自 [`javascript-analyzer`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils) 仓库的实用程序和基类。

### 💬抽象语法树

JavaScript 分析器将处理代码解决方案的[抽象语法树(AST)](https://en.wikipedia.org/wiki/Abstract_syntax_tree) 。有[和其他](https://www.metaborg.org/en/latest/)的方法来编写分析器，但是对于本文来说，AST 解析是必由之路。

```
yarn add @typescript-eslint/parser @typescript-eslint/typescript-estree 
```

[TypeScript ESLint 团队](https://github.com/typescript-eslint/typescript-eslint)已经构建了一个很棒的解析器，它输出一个 [ESTree](https://github.com/estree/estree) ，一种特定格式的标记和关于输入代码的信息。它可以与`JavaScript`和`TypeScript`一起工作，因此非常适合我们的用例。我更喜欢使用这种类型的树，因为它允许与其他工具互操作。

`parser`处理 eslint 配置，然后调用`typescript-estree`包，*使用 TypeScript* 编译代码，并转换结果以匹配 [ESTree](https://github.com/estree/estree) 。您可以直接进入 [AST Explorer](https://astexplorer.net/) ，通过将上面的示例代码粘贴到输入字段并选择`@typescript-eslint/parser`来亲自尝试一下。 **⚠注**:这里的解析器版本通常不是最新的解析器。

> 你可能想知道:为什么不使用[类型脚本编译器 Api](https://github.com/Microsoft/TypeScript/wiki/Using-the-Compiler-API) ？它在 TypeScript 语言中内置了 AST 解析。还公开了很多辅助函数(比如`isIdentifer`)。
> 
> 原因是在 ESTree spec 格式中*输出*是**而不是**，这意味着你将不能用它来使用*其他*工具。事实上，TypeScript-ESTree 包实际上是在幕后使用编译器，但随后将其转换为规范，这意味着没有锁定。你不会被编译器中的变化所束缚。

### 🏃🏽‍💨运行解析器

既然包已经准备好了，让我们来解析输入代码。

```
import { parse, TSESTreeOptions } from "@typescript-eslint/typescript-estree";

const options: TSESTreeOptions = {
  comment: false,
  jsx: false
}

const program = parse(source, options)
// => Program({ body: [...], sourceType: "module", tokens: [...] }) 
```

这给了我们与您在 [AST Explorer](https://astexplorer.net/) 中看到的相同的输出，在根节点有一个`Program`和它的`body`。我们不需要其他领域，但`tokens`很有趣。在构建树时，它列出了来自源的已解析的标记。

> 💎你可以在 [`parsers/AstParser.ts`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/parsers/AstParser.ts) 中找到这个

### 🔎寻找主入口点

我们在寻找一个叫做`gigasecond`的函数。我们知道以下事情:

*   这件事必须由 T2 来做
*   它的名字*一定是*的`gigasecond`

输入代码*声明了一个函数*，就像上面的最优解一样，所以树保存了一个带有`Identifier` :
的`FunctionDeclaration`

```
export function gigasecond(date) {
  // ...
} 
```

```
{
  type: "FunctionDeclaration",
  id: {
    type: "Identifier",
    name: "gigasecond"
  },
  generator: false,
  expression: false,
  async: false,
  params: [ ... ],
  // The body is a block { ... }
  body: {
    type: "BlockStatement",
    body: [ ... ]
  }
} 
```

这是它可以搜索的东西。在 AST 中*搜索*最常见的方式是通过*走过*那棵树。您从某个节点(通常是根/程序)开始，并访问每个项目。

我们知道我们解析的`EStree`与 [`eslint`](https://github.com/eslint/eslint) 和 [`eslint`](https://github.com/eslint/eslint) 兼容**，就像 [`prettier`](https://github.com/prettier/prettier) 可以识别(并转换)代码一样。** 

```
# Not a dev dependency!
yarn add eslint 
```

```
import { TSESTree } from "@typescript-eslint/typescript-estree"
import { traverse } from 'eslint/lib/util/traverser'

traverse(program, {
  enter(node: TSESTree.Node) {
    // ...
  }
}) 
```

在编写这段代码时，TypeScript 会抱怨这个库没有类型，不幸的是，在编写这段代码时，情况仍然如此。然而，你可以[复制我写的这个`declarations.d.ts`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/declarations.d.ts) 以便得到类型完成。

程序中的*每个节点*都会调用`enter`方法。在`enter`块中，您的“TraverserContext”公开了两个方法:

*   `this.skip()`:跳过进一步遍历的节点，这意味着它将不会访问当前节点的任何其他键(以及子节点);
*   `this.break()`:完全停止横移。

寻找入口点现在很简单。

```
import { TSESTree, AST_NODE_TYPES } from "@typescript-eslint/typescript-estree"

let entry: TSESTree.FunctionDeclaration | undefined = undefined

traverse(program, {
  enter(node: TSESTree.Node) {
    switch (node.type) {

      // function name() {}
      case AST_NODE_TYPES.FunctionDeclaration:
        if (node.id && node.id.name === 'gigasecond') {
          entry = node
          this.break()
        }
        break;
    }
  }
})

entry
// => FunctionDeclaration({
//      id: { type: "Identifier", name: "gigasecond" }, ...
//    }) 
```

不幸的是，*以上的步行者只有*找到`FunctionDeclaration`并且在等价代码上失败，使用`ArrowFunctionExpression`或`FunctionExpression`。稍后会详细介绍。

> 💎你可以在 [`analyzers/utils/extract_main_method.ts`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/extract_main_method.ts) 中找到这个

### 🔎寻找顶级常数

该代码查找两个组件中的第一个。现在它还需要找到第二个。一个顶级的`const`，名字不知道。

```
const GIGASECOND_IN_MS = (10 ** 9) * 1000 
```

```
{
  type: "VariableDeclaration",
  declarations: [
    {
      type: "VariableDeclarator",
      id: {
        type: "Identifier",
        name: "GIGASECOND_IN_MS"
      },
      init: {
        type: "BinaryExpression",
        operator: "*",
        // Left is another BinaryExpression with **
        left: { ... },
        // Right is a Literal
        right: { ... }
      }
    }
  ],
  kind: "const"
} 
```

这里没有什么是*特别*有用的。考虑到它需要接受的数据的多样性，我不能指望`init`是某种类型。名称也不固定，因为它没有经过`export` ed，因此没有经过测试。

*然而*，这里有一些约束会有所帮助:

*   它必须是顶级常量
*   它不能*而不能*命名为`gigasecond`
*   在一个最优解中，实际上只有*个不是`entry`的*顶级常数，

```
type FoundConst = { kind: TSESTree.VariableDeclaration['kind'] }
  & TSESTree.VariableDeclarator

let bigNumber: FoundConst | undefined = undefined

traverse(program, {
  enter(node: TSESTree.Node) {
    switch (node.type) {

      // const NAME = ...
      case AST_NODE_TYPES.VariableDeclaration:
        const declaration = node.declarations.find(
          (declaration) => declaration.id && declaration.id.name !== 'gigasecond')
        )

        if (declaration) {
          bigNumber = { kind: node.kind, ...declaration }
          this.break()
        }

        break;

      default:
        // This doesn't declare a variable, so skip the node
        this.skip()
    }
  }
}) 
```

稍后，我可以检查`bigNumber['kind']`并确保它是`const`，或者附加一条消息说`const`是首选。

> 💎你可以在 [`analyzers/utils/find_top_level_constants.ts`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/find_top_level_constants.ts) 中找到这个

## 算法

现在我找到了`entry`点，我可以算出论点的名字是什么(`date`)。因为我也知道顶级常数，所以我知道这个常数的名字是什么`GIGASECOND_IN_MS`。

```
new Date(...) 
```

这里没什么特别的。它是一个`new`表达式，第一个参数是一个表达式。

```
{
  type: "NewExpression",
  callee: {
    type: "Identifier",
    name: "Date"
  },
  arguments: [ ... ]
} 
```

```
let newDate: TSESTree.NewExpression | undefined = undefined

traverse(program, {
  enter(node: TSESTree.Node) {
    switch (node.type) {

      // new Date(...)
      case AST_NODE_TYPES.NewExpression:
        if (
          node.callee.type === AST_NODE_TYPES.Identifier
          && node.callee.name === 'Date'
        ) {
          newDate = node;
          this.break()
        }
        break;

      default:
        // This doesn't declare a variable, so skip the node
        this.skip()
    }
  }
}) 
```

> 💎你可以在 [`analyzers/utils/find_new_expression.ts`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/find_new_expression.ts) 中找到这个

内部表达式的类型为`BinaryExpression`。在 EStree 兼容输出中，带有两个*(如`+`、`-`、`*`)成分的 and 运算符是二进制表达式，而带有一个*(如`~`、`!`)成分的 and 运算符是一元表达式。** 

```
date.getTime() + GIGASECOND_IN_MS 
```

```
{
  type: "BinaryExpression",
  operator: "+",
  left: {
    type: "CallExpression",
    callee: {
      type: "MemberExpression",
      object: {
        type: "Identifier",
        name: "date"
      },
      property: {
        type: "Identifier",
        name: "getTime"
      }
    },
    arguments: []
  },
  right: {
    type: "Identifier",
    name: "GIGASECOND_IN_MS"
  }
} 
```

相当多的东西我们已经看到了，也有一些新的类型。让我们看看那些。

### 物体的属性

当解析器遇到对象属性访问器(`object.property`)时，它被解析为`MemberExpression`。根据书写方式的不同，该属性要么是一个`Identifier`，要么是一个`Literal`。

```
date.getTime
// ^       ^
// object  property
// |       |
// |       identifier (name = getTime)
// identifier (name = date)

date['getTime']
// ^       ^
// object  property
// |       |
// |       literal (value = getTime)
// identifier (name = date) 
```

### “执行”一个属性

如果`MemberExpression`后面有括号，那么整个表达式将被解析为`CallExpression`的子元素。跟在*标识符*后面的括号也是这种情况。

```
date.getTime ( )
// ---------| ^ |
// ^        | argument(s) of call expression
// member expression
//              |
// -------------|
// call expression

gigasecond(INPUT)
// ------|   ^   |
// ^     | argument of call expression
// identifier    |
//               |
// --------------|
// call expression 
```

### 匹配标识符

源代码提供了两个标识符，我需要查找并匹配它们:

*   千兆秒的第一个参数(用于`arg.getTime()`)
*   顶级常量(用于`time + CONSTANT`)

```
 const argumentName = entry.id.name
// => "gigasecond"
const constantName = bigNumber.id.name
// => "GIGASECOND_IN_MS"

let optimalExpression: boolean = false

// NOTE: passing in the newDate as root, so this is a subtree traversal!
traverse(newDate, {
  enter(node: TSESTree.Node) {
    switch (node.type) {

      // new Date(x.z() + y)
      case AST_NODE_TYPES.BinaryExpression:
        this.break()

        if (node.operator !== '+') {
          optimalExpression = false;
          return;
        }

        // This allows the order to be reversed
        const leftType = node.left.type
        const constSide = leftType === AST_NODE_TYPES.Identifier
          ? node.left
          : node.right
        const expressionSide = leftType === AST_NODE_TYPES.CallExpression
          ? node.left
          : node.right

        if (constSide === expressionSide) {
          // throw new Error("not optimal! this is not x.z() + y")
          optimalExpression = false
          return
        }

        if (constSide.id.name !== constantName) {
          optimalExpression = false
          return
        }

        const { object, property } = expressionSide.callee
        optimalExpression =
          object.type === AST_NODE_TYPES.Identifier
          && object.name === argumentName
          && ((
            property.type === AST_NODE_TYPES.Identifier
            && property.name === 'getTime'
          ) || (
            property.type === AST_NODE_TYPES.Literal
            && property.value === 'getTime'
          ))

      break;
    }
  }
}) 
```

> 💎您可以在以下位置找到它:
> 
> *   [`analyzers/utils/find_member_call.ts`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/find_member_call.ts) ，
> *   [`analyzers/utils/is_binary_expression.ts`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/is_binary_expression.ts) ，
> *   [`analyzers/utils/is_identifier.ts`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/is_identifier.ts) ，以及
> *   [`analyzers/utils/is_literal.ts`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/is_literal.ts) 。

## ✅自动化师徒

当所有这些部分放在一起时，它就是千兆秒的分析器。还有一些事情要检查:

*   `bigNumber.kind`等于`"const"`吗？如果没有，请添加注释
*   `GIGASECOND_IN_MS`的值是否使用了其中一个理解？如果没有，添加注释。
*   难道*到`gigasecond`只有一个*的说法吗？确保它不是一个`...splat`参数，并且没有`value = "default"`。
*   `gigasecond`是否实际出口？`export`是直列吗？如果没有，添加注释。

既然提到了第一个(`kind`相等检查)，而第二个与`new Date(...)`调用的*内部*表达式非常相似，我就不再赘述如何实现它们了。如果你需要一些灵感，可以查看[千兆秒分析器源代码](https://github.com/exercism/javascript-analyzer/tree/40116841771cc3aeb6d3fbe645495be0a3a405a5/src/analyzers/gigasecond)。第三个是为`parameters`测试`entry`。

至于`export` s，这些都是由 [`💎 extract_export`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/extract_export.ts) 处理的，不过我会给你看一下它的大意。

### 📦测试出口

`JavaScript`和`TypeScript`的出口基本上有三种。那些使用核心语言功能(阅读:使用关键字)的是最简单的:

```
export const inline = {}
export default class InlineClass {}
export default defaultSpecifier
export { specifier }
export { specifier as renamed } 
```

`default` `export`有自己的令牌类型`ExportDefaultDeclaration`。

```
{
  type: "ExportDefaultDeclaration",
  declaration: {
    // ...
  }
} 
```

没有`default`修饰符的类型是`ExportNamedDeclaration`。

```
{
  type: "ExportNamedDeclaration",
  declaration: {
    // ...
  }
} 
```

属性是有点棘手的地方。内联的`export`语句，不管它们是否是默认的，后面都跟有相同的标记类型，就好像它们没有`export`关键字一样，类似于在`CallExpression`中写圆括号包装表达式的方式。

#### 内联出口

这意味着第一个例子是一个带有单个`VariableDeclaractor`的`VariableDeclaration`:`id`是一个带有`name = "inline"`的`Identifier`，而`init`是一个`ObjectExpression`。类似地，第二个例子是一个带`id`的`ClassDeclaration`和一个带`name = "InlineClass"`的`Identifier`。

#### 说明符出口

第三个有一个带有`name = "defaultSpecifier"`的`Identifier`类型的`declaration`。这类似于`inline`出口。

然而，*做的第四和第五**不是**有`declaration`有*的属性。相反，它们有一个`specifiers`属性，在本例中只有一个项目:

```
{
  type: "ExportSpecifier",
  local: {
    type: "Identifier",
    name: "specifier"
  }
  exported: {
    type: "Identifier",
    name: "specifier" // or "renamed"
  }
} 
```

使用`local`属性确定*导出什么*(内部名称是什么)以及`exported`属性确定*如何导入*(导出名称是什么)。

#### CommonJS 出口

最后，有些导出不使用关键字，而是使用(据我所知)失效的`module.exports`。

```
module.exports = singleExport
module.exports = { specifier }
module.exports.default = defaultExport
module.exports.renamed = specifier 
```

由于这些不使用关键字，它们被解释为`ExpressionStatement` s，因为它们是`AssignmentExpression` s。下面是重要属性和表示的快速概览表:

| 表示 | 类型 | 支柱 | 价值 |
| --- | --- | --- | --- |
| `module.exports.renamed = specifier` | `AssignmentExpression` |  |  |
|  |  | `operator` | `"="` |
|  | `Identifier` | `right` | `"specifier"` |
|  | `MemberExpression` | `left` | `🔽 module.exports.renamed 🔽` |
| `module.exports.renamed` | `MemberExpression` |  |  |
|  | `Identifier` | `property` | `"renamed"` |
|  | `MemberExpression` | `object` | `🔽 module.exports 🔽` |
| `module.exports` | `MemberExpression` |  |  |
|  | `Identifier` | `property` | `"exports"` |
|  | `Identifier` | `object` | `"module"` |

还有一种使用`object['accessor']`的变体，其中`accessor`不是`Identifier`而是`Literal`，但在其他方面是相同的。

## 🔀测试变量

如前所述，用 JavaScript 和 TypeScript 编写函数有很多种方法。在分析器的[源代码](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils)中有一个实用方法 [`💎 extract_main_method`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/extract_main_method.ts) 。它可以检测以下变化:

```
function name() {}
// FunctionDeclaration

const name = () => {}
// ArrowFunctionExpression

const name = function() {}
// FunctionExpression

export default {
  name: () => {}
}
// ExportDefaultDeclaration + ObjectExpression + (Arrow)FunctionExpression 
```

和 TypeScript 的特定变体(但它们对两者都有效)

```
class Foo {
  name() {}
}
// MethodDefinition + FunctionExpression

class Foo {
  static name = () => {}
  static name = function name() {}
}
// ClassProperty + (Arrow)FunctionExpression 
```

## 行走的打字稿树

正如你已经注意到的，到目前为止我们所做的只是检查 JavaScript 代码，并展示如何解析和遍历。为了修改解决方案，以便可以用它来解析 TypeScript 代码，在 walker 中有一个要更改，还有一些额外的属性要测试。

### 🔑访客钥匙

当`traverser`遍历树时，它决定“遍历”哪些节点🚶🏽‍”基于一组名为`visitor keys`的按键。因为`TypeScript`是`JavaScript`的*超集*，所以它有相同的键，还有一些键。

```
import { visitorKeys } from "@typescript-eslint/parser/dist/visitor-keys"

traverse(root, {
  enter(node: Node) {
    // ...
  },

  visitorKeys
}) 
```

如果您查看那个文件的[源，您会看到它实际上导入了](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/parser/src/visitor-keys.ts) [`eslint`访问者键](https://github.com/eslint/eslint-visitor-keys)(为了访问所有的 JavaScript 键)并添加了特定的类型脚本键。

### 📖键入注释

这些很有趣。

```
class Gigasecond {
  constructor(private readonly date: Date) { }
} 
```

```
{
  type: "ClassDeclaration",
  id: {
    type: "Identifier",
    name: "Gigasecond"
  },
  // everything inside the class { body }
  body: {
    type: "ClassBody",
    body: [
      {
        // a constructor is a regular method definition...
        type: "MethodDefinition",
        key: {
          type: "Identifier",
          name: "constructor"
        },
        value: {
          type: "FunctionExpression",
          params: [{ /*...*/ }],
          generator: false,
          expression: false,
          async: false,
          body: { /*...*/ }
        }
        computed: false,
        static: false,  // (typescript static keyword)
        // ... but with a special kind
        kind: 'constructor'
      }
    ]
  }
} 
```

上面的内容在 JavaScript 等价物上没有特殊的属性，但那是因为除了在`constructor` :
的`params`中，在源中没有*类型注释*

```
{
  type: "Identifier",
  name: "date",
  // : ...
  typeAnnotation: {
    type: "TSTypeAnnotation",
    typeAnnotation: {
      // Readonly
      type: "TSTypeReference",
      typeName: {
        type: "Identifier"
        name: "Readonly"
      },
      // <...>
      typeParameters: {
        type: "TSTypeParameterInstantiation",
        // Each type between the < brackets >
        params: [
          {
            type: "TSTypeReference",
            typeName: {
              type: "Identifier",
              name: "Date"
            }
          }
        ]
      }
    }
  }
} 
```

一些重要观察结果:

*   类型注释有自己的访问者键`typeAnnotation`，
*   所有的 TypeScript 节点都以`TS`开头，
*   泛型只是一个既有一个`typeName`又有一个或多个`typeParameters`的`TSTypeReference`。
*   剥离类型是*几乎*和移除`typeAnnotation`键一样容易，这是*几乎*正是[巴别塔的`preset-typescript`](https://babeljs.io/docs/en/babel-preset-typescript) 所做的。

### 类属性

在 TypeScript 中，您可以用关键字`private`和`readonly`来注释类属性。此外，它们可以有一个类型(在这个例子中是`Date`)。

```
class Gigasecond {
  private readonly futureDate: Date
} 
```

```
{
  type: "ClassProperty",
  key: {
    type: "Identifier",
    name: "futureDate"
  },
  computed: false,
  static: false,            // static keyword
  readonly: true,           // readonly keyword
  accessibility: "private", // private, protected, public keywords
  typeAnnotation: {
    type: "TSTypeAnnotation",
    typeAnnotation: {
      type: "TSTypeReference",
      typeName: {
        type: "Identifier",
        name: "Date"
      }
    }
  }
} 
```

打字稿关键字`private`和`readonly`直接修改 *`ClassProperty`* ，但打字又在`typeAnnotation`上。如果源代码中省略了类型注释(读:隐式`any`)，那么`typeAnnotation`键就不会出现在 AST 上。

### ↩返回类型

我们现在要看的最后一种类型是函数`return`类型。大多数其他的类型注释只是这个和前面提到的类型注释的变体。

```
class Gigasecond {
  date(): Date {
    // ...
  }
} 
```

```
{
  type: "MethodDefinition",
  key: {
    type: "Identifier",
    name: "date"
  },
  value: {
    type: "FunctionExpression",
    generator: false,
    expression: false,
    async: false,
    body: { /*...*/ },
    params: [],
    returnType: {
      type: "TSTypeAnnotation",
      typeAnnotation: {
        type: "TSTypeReference",
        typeName: {
          type: "Identifier",
          name: "Date"
        }
      }
    }
  },
  computed: false,
  static: false, // static keyword
  kind: "method"
} 
```

你可能已经注意到了，`MethodDefinition`上的`typeAnnotation`是**而不是**。这是因为类的方法定义实际上将函数表达式`(): Date { ... }`绑定到标识符`date`。

在`FunctionExpression`上你可以找到以前没有遇到过的类型注释`returnType`。其结构与`ClassProperty`的`typeAnnotation`相同。

## 结论

将代码解释为抽象语法树并找出某些属性是一个很大的树遍历过程；因为某些 AST 解析器的输出格式有规范，所以您可以自己编写工具。

本文的内容以不同的格式用于自动批准`gigasecond`练习，假设学生提供了最优解的精确*变化*。如果学生没有提供最佳解决方案，有足够的表面来钩住分析器的发现，以提供有意义的全面评论。

[![Photo of Erasmusbrug, Rotterdam, The Netherlands, displaying gray concrete bridge near buildings.](img/7da03156ef2830554a417b433b0c7764.png "Photo by Miles Vanderlooven (https://unsplash.com/@mylesuk) on Unsplash (https://unsplash.com/)")](https://res.cloudinary.com/practicaldev/image/fetch/s--bfFQ188W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fm0tv2c3j8ij5ur2ejf9.jpg)

## 引用

*   [日期](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)
*   [日期`#getTime`T2】](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getTime)
*   [日期`.parse`T2】](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/parse)
*   [日期`#valueOf`T2】](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/valueof)

### 分析仪参考

*   [AstParser `#parse`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/parsers/AstParser.ts)
*   [T2`extractExport`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/extract_export.ts)
*   [T2`extractMainMethod`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/extract_main_method.ts)
*   [T2`findMemberCall`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/find_member_call.ts)
*   [T2`findNewExpression`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/find_new_expression.ts)
*   [T2`findTopLevelConstants`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/find_top_level_constants.ts)
*   [T2`isBinaryExpression`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/is_binary_expression.ts)
*   [T2`isIdentifier`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/is_identifier.ts)
*   [T2`isLiteral`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils/is_literal.ts)

### 练习库

*   [`problem-specifications/gigasecond`](https://github.com/exercism/problem-specifications/blob/master/exercises/gigasecond) | [规范数据](https://github.com/exercism/problem-specifications/blob/master/exercises/gigasecond/canonical-data.json)
*   [T2`javascript/gigasecond`](https://github.com/exercism/javascript/blob/master/exercises/gigasecond/gigasecond.spec.js)
*   [T2`typescript/gigasecond`](https://github.com/exercism/typescript/blob/master/exercises/gigasecond/gigasecond.test.ts)
*   [T2`javascript-analyzer/gigasecond`](https://github.com/exercism/javascript-analyzer/tree/40116841771cc3aeb6d3fbe645495be0a3a405a5/src/analyzers/gigasecond)
*   [T2`javascript-analyzer`](https://github.com/exercism/javascript-analyzer/blob/9ff332bb365bdb84c53e7b14064479ae13876fac/src/analyzers/utils)

### 套餐

*   [T2`@typescript-eslint/parser`](https://github.com/typescript-eslint/typescript-eslint)
*   [T2`@typescript-eslint/typescript-estree`](https://github.com/typescript-eslint/typescript-eslint)
*   [T2`prettier`](https://github.com/prettier/prettier)
*   [T2`eslint`](https://github.com/eslint/eslint)
*   [T2`eslint-visitor-keys`](https://github.com/eslint/eslint-visitor-keys)*