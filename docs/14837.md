# 类型安全前端开发

> 原文：<https://dev.to/ybogomolov/typesafe-frontend-development-4lbj>

> 这是我最初在媒体上发布的文章[的转贴。](https://levelup.gitconnected.com/typesafe-frontend-development-89c7600f2759)

## 前言

QCon'09 [上的东尼·霍尔为](http://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare)创造了计算机科学领域最具灾难性的发明道歉:

> 我称之为我的十亿美元错误。这是 1965 年零引用的发明。{...}这导致了数不清的错误、漏洞和系统崩溃，在过去的四十年里，这些可能已经造成了十亿美元的痛苦和损失。

我的日常工作包括使用一种语言，这种语言有两个可能的空引用位置和`null`。我说的是 JavaScript。虽然我确实喜欢 Brendan Eich 的这个宠物项目，因为它的倾斜的学习曲线、表现力和丰富的工具，但我不能允许我自己或我的同事以“纯”的形式使用它。处理`undefined is not a function`、`cannot call method 'foo' of null`等错误的代价实在太高了。我不能拿我客户的钱冒险，建议用这样的支持成本开发一个产品。

随着我越来越深入地钻研计算机科学、范畴理论和同伦类型理论，我想要我的代码:

*   合理地说，
*   为了运行时安全地执行，
*   为了有好的表现，
*   并且易于重构和支持。

**毒性警告！然而，另一方面，自由市场的存在和合格专家的明显缺乏。我可能有点偏见，但不幸的是，大多数*程序员*不是*工程师*，甚至不是数学家*。尽管如此，我还是需要找到一种方法来实现我的使命，并通过从市场原材料中创造出合格的资源来传播我的愿景。我希望编程世界成为一个更好的地方。***

 *考虑到这些前提，我选择了严格的函数式编程作为我的首选方法，选择了 TypeScript 作为我的主要语言。它两全其美:非常受欢迎(只要看看脸书等公司的成功故事就知道了)，而且有足够的额外津贴来满足我的要求。至少目前是这样。

## 什么是类型安全？

从形式的角度来看，类型安全是由编程语言语义的两个属性决定的:

1.  **(类型)保持**——程序的“良好类型化”在语言的转换规则下保持不变。
2.  **Progress**–一个良好类型化(可类型化)的程序永远不会进入一个无法进一步转换的未定义状态。

如果从学术语言翻译过来，**类型安全就是在编译时利用关于我们数据的信息来防止运行时的错误**。

最后一点。

> -斯内克，我们为什么还在这里？就为了受罪？..
> 
> 米勒一平，*金属齿轮固 V*

我确信遵循类型安全规则的开发人员应该感到舒适，并把编译器作为他最好的朋友&主要工具。但是如果开发者想写不安全的代码，*他必须承受*。他的“错误”代码不应该编译，或者他的库设计不应该易于与系统的其余部分一起使用——这不可避免地会导致同事的抱怨，然后导致繁重的重构。

玩笑归玩笑，我真的很想达到错误的程序根本不能被表达的状态。这涉及到几种技术和模式的使用。

## 键入安全模式

> **注意**由于我使用的是 Giulio Canti 的令人难以置信的 [fp-ts](https://github.com/gcanti/fp-ts) 包，我将坚持使用它的术语。

### 用`const`来进行简单的推理

你可能知道，现在 JavaScript 有三种方式声明变量:`var`、`let`和`const`。前者要求程序员记住所有的提升规则，所以在 ES6 TC39 委员会中引入了`let`和`const`关键字，这——多么令人惊讶！–不要提升。从这两点来看，只有`const`允许我们自信地思考我们的代码。我用一个简单的例子来解释一下:

```
// Line 48:
let user = await getUser();
// somewhere around line 74:
user = await getAdmin();
// Line 153:
return user; // Oops! We think that we return a user here, but it's actually an admin 
```

Enter fullscreen mode Exit fullscreen mode

有足够信心的人能说`user`变量没有在第 48 行和第 153 行之间被重新分配，而不看那些行并跟踪所有的方法调用吗？考虑一下这个:

```
// ...
// Line 48:
const user = await getUser();
// Line 74:
user = await getAdmin(); // TS2588: Cannot assign to 'user' because it is a constant.
// Line 153:
return user; // No way this will compile 
```

Enter fullscreen mode Exit fullscreen mode

好多了。

经验法则:**永远用`const`** 。

### 避开`null`和`undefined`

我引用东尼·霍尔的话开始这篇文章，不仅仅是为了显得有文化。我真的，*真的*讨厌和 JS 和 TS 的`null`们一起工作。谢天谢地，函数式编程为我们提供了一个非常棒的工具——一个`Option`单子(也称为`Maybe`)。

一个简单的例子:

```
// Bad:
function bad(registry: Registry<Module>): TransformedModule[] | null {
  const modules = getModules(registry); // => null | Module[]

  if (modules.length === 0) { return null; }

  return modules.map(doStuffWithModules);
}

// Good:
function good(registry: Registry<Modules>): Option<TransformedModule[]> {
  const modulesO = getModulesO(registry); // => Option<Module[]>
  return modulesO.map((modules: Module[]) => modules.map(doStuffWithModules));
} 
```

Enter fullscreen mode Exit fullscreen mode

所以经验之二:**如果你需要引入一个实体，它的值在程序执行的任何时候都可以不存在，使用`Option`来编码这个想法**。

### 避免抛出异常和副作用

你知道什么比回一个`null`更糟糕吗？*抛出异常*。在“传统的”JS/TS 中，没有办法用一个可能崩溃的警告来标记一个方法。在普通的 ol' Java 中，我们有`throws`关键字，但是它会导致另一系列的问题，并且(在我看来)应该在任何产品代码中被禁止。

那我们该怎么做呢？使用我们单子袋中的另一个工具——`Either`当然是单子和它的后继者！它对一个计算进行编码，这个计算可能会产生一个业务结果，也可能会因为一个**可恢复的**错误而失败。我怎么强调这一点都不为过。如果你熟悉 Scala 生态系统(即使你不熟悉)，去看看 John De Goes 写的一篇关于 ScalaZ 中 bifunctor IO 的[精彩文章。约翰很好地描述了这种方法的动机。](http://degoes.net/articles/bifunctor-io#types-of-errors)

在前端，引入一个`IO`单子是很痛苦的，因为像 React/Angular/Vue 这样的大多数框架/库都是用传统的 OOP 风格的方法编写的，并且需要相当多的样板文件来引入这个概念。然而，在代码的任何部分使用`Either`都是完全无干扰的，并且不需要对框架或库的代码进行任何修改。

> 对了，我想推荐一个很棒的模块，叫做 [remote-data-ts](https://github.com/devex-web-frontend/remote-data-ts) ，它对一个网络请求的状态进行编码。我已经用这种方法为我当前的客户构建了一个完整的渲染系统，开发人员对此很满意。去看看！
> 
> > 老实说，我已经创建了自己的`RemoteData`子类型`ApiData`，它编码了**的六个**状态，而不仅仅是**的四个**，但这是我的客户的一个特例，很容易成为另一篇完整文章的主题。

### 在编译时尽可能多地计算

这是函数式编程中我最喜欢的话题之一——类型级编码！这意味着我们以这样的方式编码我们的数据类型，即**它们只代表程序**的有效状态，而不正确的程序就是不能编译！

我常说我用的是 TDD——类型驱动开发。这显然是个笑话，但只是半个笑话。类型级编程迫使你以同样的方式考虑值和类型，你的编码从设计类型流开始——当它完成时，你的程序字面上写它自己，因为你只需要确保类型匹配。使用这种方法，您将不再看到对值进行操作的*函数和对类型参数*进行操作的*泛型类型之间的区别，这使得您的推理更加清晰明了。*

使用这种技术对所选语言的类型系统要求很高。我仍然梦想着有一天我可以用 Agda 或 Coq 之类的独立类型语言编写产品代码。但是 TypeScript 仍然有一些王牌——由于它的结构类型系统，我们可以表达像`NonEmptyList` :
这样的类型

```
type NonEmptyList<T> = T[] & { 0: T }; 
```

Enter fullscreen mode Exit fullscreen mode

当然，这并没有使 TypeScript 成为一种依赖类型的语言，但它确实是这样的:)

所以，让我分享一个例子——它可能比任何描述都更清楚:

```
// Conditional: if `T` extends `U`, then returns `True` type, otherwise `False` type
type If<T, U, True, False> = [T] extends [U] ? True : False;

// If `T` is defined (not `never`), then resulting type is equivalent to `Yep`, otherwise to `Nope`.
type IfDef<T, Yep, Nope> = If<T, never, Nope, Yep>;

// Makes keys `K` required:
type With<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>> & { [P in K]-?: T[P] };

// For typelevel tests:
const assertType = <T>(expect: IfDef<T, T, never>): T => expect;

/*
 * Actual usage
 */

// Our tested type which makes its parts optional or required, depending on passed parameters:
type ComponentProps<Routes extends string, Permissions extends string> =
  IfDef<Routes, { routes: Record<Routes, Lazy<string>>; }, { routes?: never }> &
  IfDef<Permissions, { permissions: Record<Permissions, Lazy<boolean>>; }, { permissions?: never }>;

// Code of the test using `jest`:
type Routes = 'goHere' | 'orHere';
type Permissions = 'permissionToDo' | 'permissionToBe';
type Props1 = ComponentProps<Routes, Permissions>;
type Props2 = { foo: 'bar' | 'baz';  routes: Routes; permissions: Permissions };

type Assertion1 = If<Props2, Props1, true, never>;
expect(assertType<Assertion1>(true)).toBeTruthy(); 
```

Enter fullscreen mode Exit fullscreen mode

### 最后无标签，部分求值

这只是一个设计模式，但是它的重要性高得令人难以置信。使用无标签最终编码，开发人员创建了一个定制的 [eDSL](https://wiki.haskell.org/Embedded_domain_specific_language) ，其中不正确的程序状态是不可能表达的。如果你想深入数学&原始设计，请参考 Oleg Kiselyov 等人的[初始论文](http://okmij.org/ftp/tagless-final/JFP.pdf)。

我在 TypeScript 中创建了一个实现 TF 的示例要点,所以去查看一下细节吧！

顺便说一句，有些人[建议称 2017 年为最终无标签的一年](https://typelevel.org/blog/2017/12/27/optimizing-final-tagless.html)，这意味着很多。然而，每一件沸沸扬扬的事情迟早都会走到尽头。2 月 25 日，John De Goes 将[提出他对 Scala 中 FP](https://skillsmatter.com/meetups/11945-scala-matters) 下一件大事的设想。看到结果我挺激动的。

## 结论

我希望我的文章能让你对函数式编程和类型级方法感兴趣。它真的拓宽了任何程序员的视野，并使企业拥有一个可靠的、高性能的、可支持的软件。当 WebAssembly 变得广泛可用时，我希望看到这种方法被进一步推广，我们作为前端工程师将有更多令人敬畏的工具供我们使用。谁知道呢，也许，甚至是依赖类型的语言:)

请用电报回复我 [@ybogomolov](https://t.me/ybogomolov) ，用推特回复我 [@YuriyBogomolov](https://twitter.com/YuriyBogomolov) ，或者通过 yuriy.bogomolov@gmail.com[回复我](//mailto:yuriy.bogomolov@gmail.com?subject=Typesafe%20Frontend%20Development%20feedback)。*