# TypeScript 3.7 的 5 大功能以及如何使用它们

> 原文：<https://dev.to/pimterry/the-5-big-features-of-typescript-3-7-and-how-to-use-them-1bfn>

TypeScript 3.7 版本即将发布，这将是一个大版本。

目标发布日期是 11 月 5 日，其中包括一些非常激动人心的头条新闻:

*   断言签名
*   递归类型别名
*   顶级等待
*   零合并
*   可选链接

就我个人而言，我对此非常兴奋，他们将带走一些我一直与之斗争的烦恼！

如果您没有密切关注 TypeScript 开发过程，可能不清楚这一半意味着什么，或者为什么您应该关注。让我们和他们谈谈。

## 断言签名

这是一个全新的鲜为人知的 TypeScript 特性，它允许您编写类似于[类型守卫](https://www.typescriptlang.org/docs/handbook/advanced-types.html#user-defined-type-guards)的函数，而不是显式返回它们的布尔结果。

用一个 JavaScript 例子来演示这一点最简单:

```
// In JS:

function assertString(input) {
    if (input instanceof 'string') return;
    else throw new Error('Input must be a string!');
}

function doSomething(input) {
    assertString(input);

    // ... Use input, confident that it's a string
}

doSomething('abc'); // All good
doSomething(123); // Throws an error 
```

这种模式简洁而有用，现在您不能在 TypeScript 中使用它。

在运行`assertString`之后，TypeScript 不能知道你已经保证了`input`的类型。通常人们会提出`input: string`来避免这种情况，这很好，但这也只是把类型检查问题推到了其他地方，在你只想努力失败的情况下，这个选项是有用的。

幸运的是，很快我们就会:

```
// With TS 3.7

function assertString(input: any): asserts input is string { // <-- the magic
    if (input instanceof 'string') return;
    else throw new Error('Input must be a string!');
}

function doSomething(input: string | number) {
    assertString(input);

    // input's type is just 'string' here
} 
```

这里的`assert input is string`意味着如果这个函数返回，TypeScript 可以将`input`的类型缩小到`string`，就像它在一个带有类型保护的 if 块中一样。

为了安全起见，这意味着如果 assert 语句不为真，那么 assert 函数要么抛出一个错误，要么根本不返回(终止进程，无限循环，等等)。

这是最基本的，但这实际上让你可以玩一些非常巧妙的把戏:

```
// With TS 3.7

// Asserts that input is truthy, throwing immediately if not:
function assert(input: any): asserts input { // <-- not a typo
    if (!input) throw new Error('Not a truthy value');
}

declare const x: number | string | undefined;
assert(x); // Narrows x to number | string

// Also usable with type guarding expressions!
assert(typeof x === 'string'); // Narrows x to string

// -- Or use assert in your tests: --
const a: Result | Error = doSomethingTestable();

expect(a).is.instanceOf(result); // 'instanceOf' could 'asserts a is Result'
expect(a.resultValue).to.equal(123); // a.resultValue is now legal

// -- Use as a safer ! that throws immediately if you're wrong --
function assertDefined<T>(obj: T): asserts obj is NonNullable<T> {
    if (obj === undefined || obj === null) {
        throw new Error('Must not be a nullable value');
    }
}
declare const x: string | undefined;

// Gives y just 'string' as a type, could throw elsewhere later:
const y = x!;

// Gives y 'string' as a type, or throws immediately if you're wrong:
assertDefined(x);
const z = x;

// -- Or even update types to track a function's side-effects --
type X<T extends string | {}> = { value: T };

// Use asserts to narrow types according to side effects:
function setX<T extends string | {}>(x: X<any>, v: T): asserts x is X<T> {
    x.value = v;
}

declare let x: X<any>; // x is now { value: any };

setX(x, 123);
// x is now { value: number }; 
```

这个还在不断变化中，所以不要把它当做确定的结果，如果你想要最终的细节，请关注[拉请求](https://github.com/microsoft/TypeScript/pull/32695)。

甚至还有[讨论](https://github.com/microsoft/TypeScript/pull/32695#issuecomment-523733928)关于允许函数断言某事*和*返回一个类型，这将让你扩展上面的最后一个例子来跟踪更广泛的副作用，但我们必须等待，看看这是如何发生的。

## 顶级恭候

Async/await 令人惊叹，并承诺使用起来更加干净。

然而不幸的是，你不能在顶层使用它们。在 TS 库或应用程序中，这可能不是你非常关心的事情，但是如果你正在编写一个可运行的脚本或者在一个 [REPL](https://www.npmjs.com/package/ts-node) 中使用 TypeScript，那么这就变得非常烦人了。如果你习惯于前端开发，那就更糟了，因为`await`在 Chrome 和 Firefox 中已经合法存在好几年了。

不过幸运的是，一个解决方案即将出台。这实际上是一个通用的 stage-3 JS [提案](https://github.com/tc39/proposal-top-level-await)，所以它最终也会在其他任何地方出现，但对于 TS devs 3.7 来说，这是奇迹发生的地方。

这个很简单，但是让我们来看另一个快速演示:

```
// Today:

// Your only solution right now for a script that does something async:
async function doEverything() {
    ...
    const response = await fetch('http://example.com');
    ...
}
doEverything(); // <- eugh (could use an IIFE instead, but even more eugh) 
```

带顶级 await:

```
// With TS 3.7:

// Your script:
...
const response = await fetch('http://example.com');
... 
```

这里有一个值得注意的问题:如果你不是在写脚本，或者使用 REPL，不要在顶层写这个，除非你真的知道你在做什么！

完全可以用它来编写在导入时阻塞异步步骤的模块。这对于一些特殊情况可能是有用的，但是人们倾向于假设他们的`import`语句是一个同步的、可靠的&相当快速的操作，如果你开始阻塞复杂的异步进程(甚至更糟，可能失败的进程)的导入，你可能很容易浪费你的代码库的启动时间。

异步模块导入的语义在一定程度上缓解了这一问题:它们是在*并行*中导入和运行的，因此导入模块在执行前会有效地等待`Promise.all(importedModules)`。Rich Harris 在这个规范的前一个版本上写了一篇优秀的文章，在这个版本改变之前，当导入按顺序运行时，这个问题更加严重)，如果你感兴趣的话，这是一篇关于风险的很好的背景阅读。

同样值得注意的是，这只对支持异步导入的模块系统有用。TS 将如何处理这一点还没有正式的规范，但这可能意味着最近的`target`配置，以及 ES 模块或 Webpack v5(其 alphas 有[实验支持](https://github.com/webpack/webpack/pull/9177))在运行时。

## 递归式别名

如果你曾经试图在 TypeScript 中定义一个递归类型，你可能会遇到这样的 StackOverflow 问题:[https://stack overflow . com/questions/47842266/recursive-types-in-TypeScript](https://stackoverflow.com/questions/47842266/recursive-types-in-typescript)。

现在，你不能。接口可以是递归的，但是它们的表达能力是有限制的，类型别名不能。这意味着现在，您需要将两者结合起来:定义一个类型别名，并将类型的递归部分提取到接口中。这是可行的，但很混乱，我们可以做得更好。

作为一个具体的例子，这是[建议的 JSON 数据的](https://github.com/microsoft/TypeScript/issues/3496#issuecomment-128553540)类型定义:

```
// Today:

type JSONValue =
    | string
    | number
    | boolean
    | JSONObject
    | JSONArray;

interface JSONObject {
    [x: string]: JSONValue;
}

interface JSONArray extends Array<JSONValue> { } 
```

这是可行的，但是额外的接口之所以存在，是因为它们需要绕过递归限制。

解决这个问题不需要新的语法，它只是消除了这个限制，所以下面的代码可以编译:

```
// With TS 3.7:

type JSONValue =
    | string
    | number
    | boolean
    | { [x: string]: JSONValue }
    | Array<JSONValue>; 
```

现在无法用`Type alias 'JSONValue' circularly references itself`编译。很快，很快...

## 零合并

除了很难拼写之外，这个很简单&容易。它基于 JavaScript stage-3 提案 T1，这意味着它也将很快进入你最喜欢的普通 JavaScript 环境，如果它还没有的话。

在 JavaScript 中，有一个通用的模式来处理默认值，并返回到已定义组的第一个有效结果。大概是这样的:

```
// Today:

// Use the first of firstResult/secondResult which is truthy:
const result = firstResult || secondResult;

// Use configValue from provided options if truthy, or 'default' if not:
this.configValue = options.configValue || 'default'; 
```

这在很多情况下都很有用，但是由于 JavaScript 中一些有趣的怪癖，它可能会让您措手不及。如果`firstResult`或`options.configValue`可以有意义地设置为`false`、空字符串或`0`，那么这段代码有 bug。如果设置了这些值，那么当它们被认为是布尔值时，它们就是 [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) ，所以无论如何都会使用回退值(`secondResult` / `'default'`)。

零合并解决了这个问题。代替上面的，你可以写:

```
// With TS 3.7:

// Use the first of firstResult/secondResult which is *defined*:
const result = firstResult ?? secondResult;

// Use configSetting from provided options if *defined*, or 'default' if not:
this.configValue = options.configValue ?? 'default'; 
```

`??`与`||`的不同之处在于，只有当第一个参数为空或未定义时，它才会转到下一个值，而不是 falsy。这修复了我们的错误。如果你将`false`作为`firstResult`传递，它将被用来代替`secondResult`，因为虽然它是假的，但它仍然被定义，这就是所需要的。

简单，但是超级有用，并且解决了一整类的错误。

## 可选链接

最后但同样重要的是，可选链接是另一个第 3 阶段的提议,它正在进入 TypeScript。

这是为了解决每种语言的开发人员都面临的一个问题:当数据结构中的部分或全部可能不存在时，如何从数据结构中获取数据？

现在，你可能会这样做:

```
// Today:

// To get data.key1.key2, if any level could be null/undefined:
let result = data ? (data.key1 ? data.key1.key2 : undefined) : undefined;

// Another equivalent alternative:
let result = ((data || {}).key1 || {}).key2; 
```

恶心！如果您需要更深入，情况会变得更糟，尽管第二个示例在运行时有效，但它甚至不会在 TypeScript 中编译，因为第一步可能是`{}`，在这种情况下`key1`根本不是有效的键。

如果你试图进入一个数组，或者在这个过程中有一个函数调用，这就变得更加复杂了。

有很多其他的方法可以解决这个问题，但是它们都很吵杂、混乱并且容易出错。使用可选链接，您可以这样做:

```
// With TS 3.7:

// Returns the value is it's all defined & non-null, or undefined if not.
let result = data?.key1?.key2;

// The same, through an array index or property, if possible:
array?.[0]?.['key'];

// Call a method, but only if it's defined:
obj.method?.();

// Get a property, or return 'default' if any step is not defined:
let result = data?.key1?.key2 ?? 'default'; 
```

最后一个案例展示了其中一些是如何紧密结合在一起的:空合并+可选链接是天作之合。

有一个问题:这将为丢失的值返回 undefined，即使它们是 null，例如在像`(null)?.key`这样的情况下(返回 undefined)。这是一个小问题，但是如果你的数据结构中有很多`null`的话，就要小心了。

* * *

就这么多！这应该概述了这些功能的所有要点，但还有许多较小的改进、修复和编辑器支持改进，所以如果你想了解细节，请看看[官方路线图](https://github.com/microsoft/TypeScript/issues/33352)。

希望这有用——如果你有任何问题，请在 Twitter 上告诉我。

既然你在这里，如果你喜欢 JS &想要增强你的调试技能，看看 **[HTTP Toolkit](https://httptoolkit.tech/view/javascript)** 。一键 HTTP(S)拦截&调试任何 JS 页面、脚本或服务器(以及许多其他工具)。

*最初发布于[HTTP Toolkit 博客](https://httptoolkit.tech/blog/5-big-features-of-typescript-3.7/)*