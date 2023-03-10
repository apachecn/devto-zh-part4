# EmberJs 中的静态类型？

> 原文：<https://dev.to/jamesbyrne/static-types-in-emberjs-26b7>

# ember js 中的静态类型？

所以您已经决定将 Typescript 添加到您的 Ember 项目中。您已经听说了静态类型的所有好处，以及它将如何提供比普通 javascript 更易维护的代码库(不会很难吧？).

在你一头扎进去之前，我建议你考虑以下几点。毕竟，这是对底层编程语言的一种改变(是的，我知道它是一个超集，稍后会有更多的介绍)，所以一些优点和缺点是可以预料的。但是从积极的方面开始，让我们先看看一些积极的方面。

> 注意，我不会在这篇文档中过多讨论职业或装饰者，如果你想了解他们，我强烈推荐 [pzuraq](https://www.pzuraq.com) 的[这篇文章](https://www.pzuraq.com/coming-soon-in-ember-octane-part-1-native-classes/)。

## 好人

### 静态打字的好处

[![](img/5c132a1a2ed1d0917549fe972ad23a18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8EEiayPZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/asgg0162nmx4sphls8tq.jpg)

> 这是我最喜欢的迷因

大概谈论最多的 Typescript 特性是它的静态类型，我们可以用它来指定我们期望从参数/变量中得到的值的类型。这反过来又使 Typescript 编译器能够捕捉编译步骤中的常见错误，从而减少进入生产环境的错误数量。

这方面的一个例子是解析一些数据结构。假设我们正在解析一个错误，但是这个错误可以有多种形式。它可以是一个字符串或包含字符串列表的对象。一个潜在的解决方案可能是这样的:

```
function getAllErrors(error = {}) {
  if (typeof error === 'string') return [error];
  if (typeof error === 'object') return error.errors;

  return [];
} 
```

乍一看，这看起来不错，我们接受一个错误，检查它的类型，如果它符合我们想要的，我们就返回它，否则我们返回一个空列表。然而这里有一个错误，如果`error`作为`null`被传递，它将使第一个`if`语句失败，但是通过后面的语句。

> 因为当然`typeof null === 'object'` …

显然这不是我们想要的。幸运的是，静态类型会捕捉到这个错误。让我们从编写类型开始(这是您应该首先做的事情)。

```
type ErrorMessage = string;

interface Error {
  errors: Array<ErrorMessage>
}

type PotentialError = any | ErrorMessage | Error; 
```

好了，现在我们对数据结构的形状有了一个很好的想法，我们可以更清楚地表达我们函数的意图。

```
function getAllErrors(error: PotentialError): Array<ErrorMessage> {
  if (isErrorMessage(error)) return [error];
  if (isError(error)) return error.errors;

  return [];
} 
```

好多了！但是等等！那些新功能是什么？`isErrorMessage`和`isError`？那些是警卫。它们允许您告诉 typescript 编译器返回的是什么类型。因此，举例来说，`isError`类型的后卫可以像这样:

```
function isError(error: PotentialError): error is Error {
  return 'errors' in error;
} 
```

现在编译器知道了错误的类型，如果我们试图改变返回语句来返回一个不在`Error`接口中的参数或者返回`errors.errors`的第一个索引，如下所示，编译器会告诉我们。

```
if (isError(error)) return error.errors[0];
//                               ^
//                               |
// Type string is not assignable to type string[] 
```

对于可维护性来说，这是一个巨大的胜利，因为我们确切地知道从我们已经编写的函数以及更重要的是我们没有编写的函数中可以期待什么。

### 文档

请注意，我们示例函数的实现现在是多么清晰。以前，由于这是一个在整个应用程序中使用的实用函数，我会写一组如下的注释:

```
/* @method getAllErrors/1
 * @return List(Object)
 *
 * If it is a simple error (a
 * string) then that will be returned as
 * the expected return type.
 *
 * If a list of errors are found within
 * the object they will be returned.
 *
 * If it is not a string or an object we
 * will return an empty list.
 */
function getAllErrors(error = {}) {
  if (typeof error === 'string') return [error];
  if (typeof error === 'object') return error.errors;

  return [];
} 
```

随着应用程序变得越来越复杂，我认为这种类型的信息是最基本的要求(稍后将详细介绍)。了解函数接受的返回类型和参数类型将让我们在使用函数时避免问题，并创建一个清晰的边界，在这个边界内我们可以重构函数本身。

现在，如果我们考虑相同的 Typescript 代码，显然不再需要这些注释。

```
function getAllErrors(error: PotentialError): Array<ErrorMessage> { 
```

更重要的是，当我们重构时，这些类型*将有*保持最新(常规注释的一个大问题),它们将通过编译器警告和编辑器工具给我们额外的信息。我说过这是最低要求的信息，所以我会简单地说一下。

Typescript 给了我们类型来回答函数的*什么*而不是*为什么*。对于非常简单的函数来说，仅仅是函数的名字就足够了，但是有时函数会非常复杂，范围有限或者有副作用。这些事情仍然应该记录在功能文档中。否则我们不知道什么时候使用一个功能，更重要的是不知道什么时候使用*。*

在仍然需要文档的情况下(函数的*为什么*是复杂的)，Typescript 生态系统提供了 [TsDoc](https://github.com/Microsoft/tsdoc) 。这是编写文档的标准方式，也适用于工具，如 vscode editor。

### 工装

Typescript 的另一个好处是其出色的编辑器集成，特别是与 [VsCode](https://code.visualstudio.com/docs/languages/typescript) 的集成，它提供了智能感知、重构工具、调试工具、林挺、文档和格式化工具。所有这些东西都会被你的编辑器和编译器发现。

> 对于 Vim 用户，查看 [tsuquyomi](https://github.com/Quramy/tsuquyomi) 以获得一套类似的工具

### 一个超集

也许打字稿最大的特点，毫无疑问是其增长的最大原因之一。它是 javascript 的超集，这意味着任何 javascript 也是有效的类型脚本。这使得成为一名 typescript 开发人员就像将您的`.js`文件重命名为`.ts`一样简单。

从那里开始，您可以逐渐键入越来越多的代码，并使编译器更加严格，直到您的整个代码库(或您想要的任何数量)完全按照 Typescript 编写。这对于现有的项目来说是巨大的，这些项目不想从头再来，但是仍然想要类型化语言的好处。

> 请注意，您将需要配置某种构建管道，但我认为这是独立的

## 坏了

### 突发变化

不幸的是，Typescript 并没有严格遵循[sever](https://github.com/Microsoft/TypeScript/issues/14116)，这在 Ember 生态系统中是理所当然的。由于市场压力，突破性的改变通过小版本引入，尽管在那之后他们确实保持了一些 semver 的外观。来自 [niieani](https://github.com/Microsoft/TypeScript/issues/14116#issuecomment-280915422) 的有用图表/评论显示了我们应该如何处理这个问题。

```
 marketing
           ∨
TypeScript 2.34.2.1
             ∧∧ ∧ ∧
          major ∧ patch
                ∧
              minor 
```

这是一个生态系统中的问题，它希望事情遵循服务器，而不是在小版本上做出突破性的改变。但这暴露了一个更大的问题。除了 Ember 本身(尽管这适用于所有框架)，Typescript 有可能需要你的代码库的最大重构。

虽然不太可能，但对 Typescript 的任何基本更改都会对您的代码库产生连锁影响，并且可能会影响很大一部分代码库。这使得 semver 的缺乏更加令人担忧，并且是在直接投入之前必须仔细考虑的事情。

### 约定俗成&专长

另一个我认为经常被忽略的因素是惯例和你团队的专业知识。当然，Typescript 是一组超级 javascript，但这并不意味着您的团队可以利用它。例如，我们前面的例子中出现的一个问题。

> 我们上面声明的类型应该存在于一个全局类型文件中吗？

你会如何回答这个问题？我可以肯定地看到在应用程序的其他地方使用了`PotentialError`类型，Typescript 为我们提供了一种在项目中声明全局类型的方法。

这听起来很合理也很有用，但是我的回答是:

> 不，它们太具体了，任何全局`Error`类型都必须更加通用。这些类型特定于文件中函数所期望的结构。在最好的情况下，它们可能是全局类型名称空间中更广泛类型的某个子类型。

在我职业生涯的早期，我有幸使用过各种不同的语言和框架，包括类型化和非类型化语言。但是并不是所有的团队都有这样的经验，并且来自 Javascript 背景，确保你的团队拥有做出正确决策所需的专业知识是很重要的。否则你会制造一场混乱。

### 漏打

不幸的是，并不是每个库都有一套刚出炉的类型。因此，您可能会遇到公共库没有类型的问题。“当然没问题，这是一个超集，它仍然会工作”我听到你说。

虽然 Typescript 编译器和工具能够在配置更严格的情况下最好地展示它们的东西。当试图编译没有类型或至少没有类型安全的代码时，这将导致错误和警告，这种情况违背了要点。

现在有一些很棒的项目，如[明确类型化的](https://definitelytyped.org/)，它提供了一个巨大的类型定义库，但即使这样，普通的库，如 [ember-concurrency](http://ember-concurrency.com/docs/introduction/) 和 [Ember Data Storefront](https://embermap.github.io/ember-data-storefront/) 也没有类型。这意味着要么通过`.d.ts`(一个类型定义文件)添加你自己的类型，要么在你应该依赖的类型安全上妥协。

## 过渡到打字稿

既然你已经做到了这一步，并且没有被吓倒，那么让我们来概述一个简单的方法，将你现有的 Ember 应用程序转换为使用 Typescript。

首先，我们将安装优秀的项目 [`ember-cli-typescript`](https://github.com/typed-ember/ember-cli-typescript) ，它将负责为我们建立&转换我们的打字稿文件。这是设置 Typescript 的最简单的方法，但是它有我们应该知道的[限制](https://typed-ember.github.io/ember-cli-typescript/docs/ts-guide/current-limitations)。

至于输入应用程序本身，Mike North 建议采取以下步骤:

1.  从允许隐式`anys`开始，将所有的`.js`文件重命名为`.ts`
2.  添加尽可能多的类型信息，但不要涉及细节。选择显式`any`。班隐`any`
3.  详细介绍某些常用的模块(服务、模型等)

通过遵循这些步骤，我们可以将整个应用程序转换为 typescript，而不会干扰团队的其他成员或妨碍功能工作。

这就是你要的，用最简单的方式在 Ember 打印出来！