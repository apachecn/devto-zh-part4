# 你可能会错过对 Typescript 的介绍

> 原文：<https://dev.to/michaeljota/the-introduction-to-typescript-you-may-be-missing-mnp>

*照片由 Amar Yashlaha 在 Unsplash 上拍摄*

去年我写了一篇这样的关于 Angular 的帖子，我认为它对新的角落真的很有帮助。我想对接触打字稿的新人做同样的事情。

## 什么是 Typescript？

Typescript 是一种由微软开发和维护的开源编程语言。它是 JavaScript 的严格超集，添加了可选的静态类型。它于 2012 年 10 月 1 日首次推出，差不多 7 年了，经过多次发布，现在是 3.7 版本。因为 Typescript 没有遵循语义版本，所以每个版本都引入了新功能和一些突破性的变化。直到今年，发布周期是每两个月一个新版本，但他们已经转移到 3 个月的发布周期，以提高每个版本的稳定性。它是用 TS 开发的，并使用 TSC 编译成 JS，这意味着它可以自行编译。

在早期版本中，Typescript 在 JS 中引入了不稳定的概念，比如类、模块等等。这些概念导致了一种误解，即微软试图创造一种新的脚本语言来取代 JavaScript，而不是一种改进 JavaScript 本身的语言。在新版本中，Typescript 采用了新版本 ECMAScript 规范的新特性，并改进了与普通 JavaScript 文件的集成。

## Typescript 是 JavaScript 的超集意味着什么？

简单地说，每一个 JavaScript 源文件*都应该正常工作*。但是，情况并不总是这样。为了做到这一点，您应该禁用 Typescript `strict`类型检查，这是默认启用的，因为它是在[版本 2.3](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-3.html#new---strict-master-option) 中引入的。但是，这样你就不会像你应该的那样利用类型检查。如果你试图用 Typescript 编译器编译任何 JS 源文件，只需将扩展名从`.js`改为`.ts`，你可能会发现来自`tsc`的一些抱怨。

## 介绍 Typescript 编译器(`tsc`)和类型注释

我们将看到一个例子。首先，我们要安装`typescript`。您可以全局安装它，但对于本例，我将创建一个 npm 项目。确保您使用的是最新的节点 LTS。

1.  `$ mkdir ts-example`

2.  `$ npm init -y`

3.  `$ npm i typescript`

4.  `$ touch fibonacci.ts`

5.  我们粘贴代码:

```
 function fibonacci(num, memo) {
     memo = memo || {};

     if (memo[num]) return memo[num];
     if (num <= 1) return 1;

     return (memo[num] = fibonacci(num - 1, memo) + fibonacci(num - 2, memo));
   } 
```

Enter fullscreen mode Exit fullscreen mode

1.  `$ npx tsc`

2.  查看控制台中的错误:

```
 fibonacci.ts:1:10 - error TS7023: 'fibonacci' implicitly has return type 'any' because it does not have a return type annotation and is referenced directly or indirectly in one of its return expressions.

              ~~~~~~~~~

   fibonacci.ts:1:20 - error TS7006: Parameter 'num' implicitly has an 'any' type.

   1 function fibonacci(num, memo) {
                        ~~~

   fibonacci.ts:1:25 - error TS7006: Parameter 'memo' implicitly has an 'any' type.

   1 function fibonacci(num, memo) {
                             ~~~~ 
```

Enter fullscreen mode Exit fullscreen mode

1.  或者，如果您正在使用编辑器:

```
 function fibonacci(num, memo) {
            ~~~~~~1   ~~2  ~~3
     memo = memo || {};

     if (memo[num]) return memo[num];
     if (num <= 1) return 1;

     return (memo[num] = fibonacci(num - 1, memo) + fibonacci(num - 2, memo));
   } 
```

Enter fullscreen mode Exit fullscreen mode

这是一个有效的 JS 函数。一个斐波那契函数，它将先前调用的结果存储在 memo `object`中，然后在一次递归调用中再次返回，直到它到达终点。 [1](https://dev.to1) 虽然 Typescript 报告了 3 个错误，但它们很可能都是相同的，但我们将逐个检查它们:

1.  `'fibonacci' implicitly has return type 'any' because it does not have a return type annotation and is referenced directly or indirectly in one of its return expressions.`
2.  `Parameter 'num' implicitly has an 'any' type.`
3.  `Parameter 'memo' implicitly has an 'any' type.`

那是什么意思？你可能会问。它告诉你，它不能做它应该做的，输入。它没有关于什么是`fibonacci`返回类型的信息，也没有应该是什么参数的信息。但是，我们可以解决这个问题，因为我们知道这些信息，我们可以向 TS 提供这些信息。

1.  `fibonacci`函数应该返回一个数字。
2.  `num`参数应该是一个数字。
3.  `memo`参数应该是一个对象。

现在，我们使用 Typescript 类型注释向`tsc`提供信息。Typescript 中的类型批注是在变量声明之后开始的描述，后跟一个冒号和一个类型。对于函数和方法，该描述是针对返回类型的，而不是针对函数本身的。

```
 var a: number;
  // "var a" is the regular JS variable declaration, and ": number" is the type annotation.
  function a(): string {/* */}
  // "function a() {}" is the regular JS function declaration, and ": string" is the return type annotation. 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们将它用于我们的 Fibonacci 示例，并再次运行`tsc`命令，或者如果我们在编辑器中:

```
function fibonacci(num: number, memo: object): number {
  memo = memo || {};

  if (memo[num]) return memo[num];
      ~~~1              ~~~2
  if (num <= 1) return 1;

  return (memo[num] = fibonacci(num - 1, memo) + fibonacci(num - 2, memo));
          ~~~3
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了第二个错误。而这往往是人们开始抱怨的时候。

*   你知道吗？去他妈的！这种简单的代码【Typescript 不懂又不是我的错。良好的...也许吧，但是有没有注释，你能更好地理解函数吗？有了类型注释，你可以直接读出你需要的类型，以及如何使用它。
*   *我可以用 JSDocs* 注释我的代码。是的，你可以，但是没有什么可以检查你的代码，但是也许 Typescript 编译器本身，稍后会有更多的内容，如果你已经打算使用`tsc`为什么不写`ts`文件呢？

## 介绍接口、类型和类型别名

回顾错误，我们可以注意到实际上是同样的错误:`Element implicitly has an 'any' type because the expression of type 'number' can't be used to index type '{}'. No index signature with a parameter of type 'number' was found on type '{}'.`这可能不是对问题的最佳描述，但可能是你经常阅读的内容。`tsc`在抱怨`memo`拥有`object`型。在`strict mode`中，`object`类型等于一个空对象(`{}`)，访问没有在变量类型中声明的属性会将该属性标记为 any，因为 Typescript 不知道它正在类型化，但它可以假设它确实存在，但不在`strict mode`中。因为我们在`strict mode`中，Typescript 告诉我们:*嘿，你正试图访问一个我不知道的属性，我可以像任何一样给你，但你也不想那样。所以，我需要你给出关于你试图得到的这个属性的正确的输入信息。*

我们将使用类型别名来解决这个问题。Typescript 有 3 种获取类型信息的方式:

*   JS `class`有自己的类型信息，也是应用程序可用的有效构造函数。你可以在其他类中扩展和实现类，注意你可以实现许多类，但是只能扩展一个。

*   一个`interface`。这是一个 TS 特性，允许你声明一个对象的*形状*。这在运行时是不存在的，所以你不能调用它或者把它赋值为一个`class`，不要担心，如果你试图这样做，TS 会抱怨的。您可以在接口声明中扩展类和其他接口。您可以在一个`class`中实现许多接口。

*   一个`type`。这是 TS 的另一个特性，允许你声明一个对象的*形状*。这在运行时也不存在，但是你不能扩展它，只有当`type`有静态已知成员时才能实现它。使用`type`，你可以使用类型修饰符、映射器、条件和其他复杂的类型结构，比如*类型别名*。

因此，*类型别名*是帮助我们更好地表达复杂对象形状的`type`。`memo`有多复杂？你可能会问。当你不知道一个物体的所有属性时，表达它实际上有点复杂。`memo`的属性是在运行时计算的，它们不是静态已知的*。Typescript 有几个内置的官方助手，但由于某种原因，它们没有在官方文档中列出。这些别名包括:`NonNullable`、`ReturnType`、`InstanceType`、`Readonly`和`Record`。最后一个是我们用来解决问题的:* 

```
function fibonacci(num: number, memo: Record<number, number>): number {
  memo = memo || {};

  if (memo[num]) return memo[num];
  if (num <= 1) return 1;

  return (memo[num] = fibonacci(num - 1, memo) + fibonacci(num - 2, memo));
} 
```

Enter fullscreen mode Exit fullscreen mode

通过`Record<number, number>`，我们告诉 Typescript】是一个具有`number`索引和`number`属性的对象。它检查了一下，现在似乎一切正常。

## 编译

我们知道`tsc`命令(或者不知道),但是它只是从 Typescript 编译(技术上讲是[*trans file*](https://www.wikiwand.com/en/Source-to-source_compiler))成普通的 JavaScript，同时检查所有类型是否正确。默认情况下，`tsc`会输出`.js`文件，不管类型是否正确。如果你看看输出，它看起来更像我们例子的第一个版本，这没问题。那是因为没有设备可以运行 TS 源代码，只有 JS 可以，所以 TS 做的就是去掉我们所有的注释，留下一个漂亮干净的 JS 文件。

`tsc`的威力在于它的配置文件 [`tsconfig.json`](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html) 和它的[编译器选项](https://www.typescriptlang.org/docs/handbook/compiler-options.html)。有了它，您就可以启用或禁用强大的特性，比如 no 在出错时发出，或者允许编译 JS 文件。

## 向前看

就像我之前说的，TS 的动作真的很快。他们最近引入了新的 ES 特性，如[可选链接](https://github.com/microsoft/TypeScript/issues/16)和[无效合并](https://github.com/microsoft/TypeScript/issues/26578)，还引入了新的 TS 特性，如[递归类型引用](https://github.com/microsoft/TypeScript/pull/33050)和[断言签名](https://github.com/microsoft/TypeScript/pull/32695)，以及一些有望在 2 月份引入 TS 3.8 的新特性。由于这些突破性的变化，使用 TS 的框架(如 Angular 或 React)不能走得那么快，所以它们通常在 TS 的新版本发布几周后才提供支持。但这正在改变，因为 TS 现在提供了一个带有稳定 API 的 RC 版本。然而，这并不能阻止你在[游乐场](https://www.typescriptlang.org/play/)中尝试新功能。

## 奖励:输入 JavaScript

您可以使用 JSDocs 为 JS 文件中的变量和方法提供类型注释。然后，您可以使用 TS 支持的编辑器(如 VS Code)来做所有的检查，并在常规 JS 文件中使用 TS 的功能。

[1]:声明一下，我是从这里得到那个函数的:[https://medium . com/developers-writing/Fibonacci-sequence-algorithm-in-JavaScript-b 253 DC 7 e 320 e](https://medium.com/developers-writing/fibonacci-sequence-algorithm-in-javascript-b253dc7e320e)。我不要求许可，我希望作者不要为此生气。谢谢你分享这个。😄。我之所以这么做，是因为斐波那契函数看起来很简单，但正如我们所见，它们很难输入。