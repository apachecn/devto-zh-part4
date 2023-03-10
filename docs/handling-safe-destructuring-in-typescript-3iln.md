# 在类型脚本中处理安全析构

> 原文：<https://dev.to/kamranayub/handling-safe-destructuring-in-typescript-3iln>

*最初在[kamranicus.com](https://kamranicus.com/posts/2019-06-26-typescript-safe-destructuring-patterns)T3 发布在我的博客上*

* * *

当从 JavaScript 迁移到 TypeScript 时，您经常会遇到似乎很难静态类型化和析构对象相关的场景。

## 析构空对象

下面的代码片段展示了这种模式，它反映了我们最近查看的一些代码的语义:

```
function transformSearchResults(payload = {}, queryParams = {}) {
  const { results } = payload;
  const { searchTerm } = queryParams;

  if (!results) {
    return payload;
  }

  if (searchTerm && isSearchPayload(payload)) {
    payload.transformedResults = results.map(r => ({
      ...r,
      anotherProp: true
    }));
  }

  return payload;
} 
```

这个函数实际上接受一个有效负载，对它进行转换，并返回修改后的有效负载对象。

具体来说，请注意函数参数中的表达式`= {}`，在 JavaScript 中，如果参数为`undefined`，它将为参数设置默认值`{}`。这样做是因为在接下来的 2 行中，我们试图析构并取出对象的一些关键道具。如果`payload`或`queryParams`是`undefined`，那么析构将会爆炸，而*我们不希望这个代码爆炸。这很合理！*

## 转换成打字稿

如果我们想将这个函数迁移到 TypeScript，为每个参数添加一个类型注释是有意义的，如下所示:

```
export interface ResponsePayload {
  results: SearchResult[] | OtherResult[];
}

function transformSearchResults(
  payload: ResponsePayload = {},
  queryParams: QueryStringParams = {}
) 
```

我们试图通过告诉 TypeScript 我们期望这些参数是什么类型来正确地注释我们的类型。我们已经用一个`results`属性声明了我们的 API 类型`ResponsePayload`。

但是唉，TypeScript 会立即抛出一个错误:

```
Property 'results' is missing in type '{}' but required in type 'ResponsePayload'. ts(2741) 
```

虽然乍一看这似乎是错误的，但 TypeScript 实际上是在这个函数中指出了一个*代码味道*。

TypeScript 不允许这样做的原因是因为`ResponsePayload`类型有一个`results`属性是*必需的*(不能未定义)。我们知道这一点是因为我们是这样输入的，大概是因为 API 不会返回 undefined。如果是的话，我们可以把`results`改成可选的。

当将析构代码转换为 TypeScript 时，将鼓励您比以前更严格地考虑代码，否则您将遇到类型错误。

在这种情况下，我的要求是:

1.  可以简化函数来为参数传递一个特定的原始值吗？

2.  如果我需要传递一个对象，我可以重构函数来避免它吗？

这些问题取决于具体的情况，但是在大多数情况下，你可以通过问这些问题来消除析构的需要。

比如，我们一步一步来。

## 简化函数参数

> 可以简化函数来为参数传递一个特定的原始值吗？

代码又来了:

```
function transformSearchResults(
  payload: ResponsePayload = {}, 
  queryParams: QueryStringParams = {}) 
{
  const { results } = payload;
  const { searchTerm } = queryParams;

  if (!results) {
    return payload;
  }

  if (searchTerm && isSearchPayload(payload)) {
    payload.transformedResults = results.map(r => ({
      ...r,
      anotherProp: true
    }));
  }

  return payload;
} 
```

快速浏览一下就会告诉我们，答案是**对`payload`没有**，但**对`queryParams`有**。

只有`payload`中的`results`被使用，但是`payload`也被变异了，所以函数需要传递对象，让我们把它放在一边。

对于`queryParams`，实际上只引用了`searchTerm`，所以让我们简化这个函数，只传递那个:

```
function transformSearchResults(
  payload: ResponsePayload = {}, 
  searchTerm?: string) 
{
  const { results } = payload;

  if (!results) {
    return payload;
  }

  if (searchTerm && isSearchPayload(payload)) {
    payload.transformedResults = results.map(r => ({
      ...r,
      anotherProp: true
    }));
  }

  return payload;
} 
```

太好了！其余的代码保持不变，我们消除了一个不必要的析构。

## 面向解构的重构方法

> 如果我需要传递一个对象，我可以重构函数来避免它吗？

现在该函数需要直接提供`payload`。我们现在可以采取两种方法:

1.  为必需的属性指定默认值
2.  不需要改变一个对象，这样我们就可以通过`results`

### 为属性提供默认值

如果我们想要保持函数中的逻辑完整，我们需要为`ResponsePayload`的所有必需属性提供一个值。

我们可以在函数中内联这样做，这对于一个小的参数列表来说很好。

```
function transformSearchResults(
  payload: ResponsePayload = { results: [] }, 
  searchTerm?: string) 
```

全部固定；因为我们为`results`提供了一个默认值，而不是`undefined`，所以 TypeScript 很高兴。

我们也可以选择为更复杂的情况提供一个默认对象:

```
const defaultPayload: ResponsePayload = {
  results: []
};

function transformSearchResults(
  payload: ResponsePayload = defaultPayload, 
  searchTerm?: string) 
```

这同样有效，但是引入了一个可能不会增加太多价值的悬空对象。我们能首先消除提供`payload`的需要吗？

### 完全删除对象

函数变异原始传入对象的事实本身就是一种气味。有些情况下这是有道理的，但在这种情况下，我们正在转换结果——我们应该只返回新映射的结果，并将对变异`payload` 的关注提升到一个更高的层次。

```
function transformSearchResults(
  results: SearchResults[] | OtherResults[], 
  searchTerm?: string) 
{
  if (searchTerm && isSearchPayload(payload)) {
    return results.map(r => ({
      ...r,
      anotherProp: true
    }));
  }

  return results;
} 
```

*快到了。*我们现在会收到一个错误，因为`isSearchPayload`正在测试有效载荷本身。我们有两个选择:

1.  修改`isSearchPayload`以检查结果
2.  将对检查有效负载的关注推高

我们当然可以做选项 1，但我更喜欢选项 2。它将最终简化`results`输入，因为我们可以*知道*结果是*搜索结果*！这也使得函数名更加准确:

```
function transformSearchResults(
  results: SearchResults[], 
  searchTerm?: string) 
{
  if (searchTerm) {
    return results.map(r => ({
      ...r,
      anotherProp: true
    }));
  }

  return results;
} 
```

我们走吧。通过简化需求和避免对象突变，我们已经完全消除了用这个函数进行析构的需要。

### 复杂性去哪了？

通过重构函数，我们将两个问题推到了调用堆栈的更高位置:

1.  这是搜索载荷(`isSearchPayload`)吗？
2.  `payload`的处理`transformedResults`

这种逻辑仍然存在，但现在它可以被提升到更高的位置。这里有一个原则叫做[命令-查询分离](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation)原则，它说:

> “每个方法要么是执行某个操作的命令，要么是向调用者返回数据的查询，但不能两者都是。换句话说，问一个问题不应该改变答案。

在我们重构这个函数之前，它违反了这个原则，因为它既*返回了新的结果*(一个查询)**又**通过改变对象(一个命令)产生了副作用。现在我们将它重构为一个“查询”函数。

如果我们检查了这个函数以前的调用方式，您会看到:

```
function (payload?: ResponsePayload) {
  if (!payload) {
    throw new Error("Missing response payload!");
  }

  payload = transformSearchResults(payload, queryParams);

  return payload;
} 
```

这应该会引发一个黄色标志，因为它再次返回对象，这意味着它可能正在对它进行变异(换句话说，“它有气味”)。

现在我们已经重构了上面的功能，一些职责被推到了更高的位置，这可能会导致这样的变化:

```
function (payload?: ResponsePayload) {
  if (!payload) {
    throw new Error("Missing response payload!");
  }

  if (isSearchPayload(payload)) {
    return {
      ...payload,
      transformedResults: transformSearchResults(payload.results)
    }
  }
} 
```

我们避免直接改变有效载荷，而是选择合并新的属性。我们现在可以选择提取这个逻辑，重构它，等等。这你可以决定！

## 处理嵌套析构

如果我们能够通过简化函数来消除析构的需要，这是一个胜利，但是如果我们真的需要安全地析构，更具体地说，以嵌套的方式，该怎么办呢？

```
function handleData(data = {}) {
  const { meta: { field1, field2, field3 } = {} } = data;
} 
```

这稍微复杂一点，因此更难键入*，应该是*。我开始欣赏 TypeScript 的一点是，它让你感觉到更多的痛苦，这反过来鼓励你后退一步，并质疑为什么代码是这样构造的。

在这个例子中，我们可以问与我们最初问的相同的问题:*我们真的需要`data`吗或者我们可以更具体一些？*

### 保管`data`

假设我们必须保留`data`作为参数。如果`meta`和所有的`field*`属性*真的可以是`undefined`* 并且被标记为真，那么这段代码就有效。

只要其中一个是非可选的，TypeScript 就会抛出一个错误，因为我们还没有提供默认值。

例如，假设`data`的类型为:

```
interface Data {
  meta: Metadata
}

interface Metadata {
  field1: string;
  field2: boolean;
  field3: object;
} 
```

`meta`不是可选的，它必须被提供，所以上面的代码会抛出一个错误，因为没有提供默认的`meta`值，也没有提供默认的`field*`。呀！

如果可能的话，最好的方法是像上面那样进行重构来简化参数。

下一个最好的方法是*去掉默认的* :

```
function handleData(data: Data) {
  const { meta: { field1, field2, field3 } } = data;
} 
```

这与我们描述的类型相匹配——没有属性可以是`undefined`,所以*没有理由*再使用默认参数了。如果这符合您的预期，就没有什么额外的工作要做了。

如果在实践中，事情有可能是`undefined`的，但是我们仍然希望确保类型保持“纯净”呢？

那么我们应该将潜在的`undefined`值的“保护”推到堆栈的更高层:

```
function checkData(data: Data | undefined) {
  if (!data) {
    throw new Error("Data shouldn't be undefined");
  }

  return handleData(data); // No longer can be undefined
}

function handleData(data: Data) {

  // This is safe to do now
  const { meta: { field1, field2, field3 } } = data;
} 
```

一般来说，你应该尽可能靠近源头。这使得下游函数更简单，并且使用 TypeScript，您可以确保它们获得不需要提供默认设置的传递值。

### 支持部分对象

让我们假设`meta`字段*可以*部分可用，我们可以这样简化并静态地输入函数:

```
interface Data {
  meta: Partial<Metadata>
}

interface Metadata {
  field1: string;
  field2: boolean;
  field3: object;
}

function (data: Data) {

  // This is safe now as `meta` can be partially undefined
  const { meta: { field1, field2, field3 } = {} } = data;
} 
```

现在我们已经*明确地*声明`meta`可以被部分定义。这做了两件事:

1.  它允许安全的析构而没有编译器错误，
2.  它告诉读者*期望*防范部分定义的字段。
3.  我们已经使[成为不可表示的非法状态](https://fsharpforfunandprofit.com/posts/designing-with-types-making-illegal-states-unrepresentable/),这意味着`Metadata`不应该有任何可选字段*理想情况下是*,但是函数可以以隔离的方式支持部分属性

静态类型是文档，现在我们已经呼吁期望`meta`可以有部分字段。它还允许我们安全地将默认对象设置为`{}`以避免析构时出错。

## 最后一招:类型断言

有一把锤子可以作为最后的手段。如果我们不能有效地重构析构或者如果我们真的需要强制这种模式来避免改变代码，我们可以使用类型断言:

```
function transformSearchResults(
  payload = {} as ResponsePayload,
  queryParams = {} as QueryStringParams
) 
```

这将迫使 TS 将空对象*视为我们想要的类型*——但是这引入了潜在的错误，因为现在我们选择退出类型安全。我们将检查`undefined`的责任放在编写函数的开发人员身上，而不是 TypeScript 编译器。

如果您在启用 ESLint 规则的情况下这样做，您将会看到一个错误:

```
Type assertion on object literals is forbidden, 
use a type annotation instead. 
eslint(@typescript-eslint/no-object-literal-type-assertion) 
```

这也是因为最好在第一时间*避开这个*。为了编译它，你需要禁用错误:

```
/*  eslint-disable-next-line @typescript-eslint/no-object-literal-type-assertion */
function transformSearchResults(
  payload = {} as ResponsePayload,
  queryParams = {} as QueryStringParams
) 
```

至少现在，您可以稍后搜索这些被禁用的错误，作为以后重新访问和重构的指示。

## 总结

当迁移到 TypeScript 时，你会遇到很多这样的问题，表面上看起来你好像在与类型系统斗争，但是我希望让你明白的是, *TypeScript 迫使你更明确地思考你的代码是如何构造的，你的意图是什么。*它将迫使你超越单一功能的范围来看待*，更全面地考虑用法和上下文。*

当您发现自己试图通过类型断言来禁用规则或解决类型脚本错误时，您需要后退一步，问问自己:

> 这是构建这段代码的最佳方式吗？还是 TypeScript 暗示我可能需要重构它？

当使用 TypeScript 时，这种思维方式是思维方式的转变，尤其是当你是从 JavaScript 过来的时候。一开始可能会很痛苦，但最终会产生更好、更简单的代码。

如果你有兴趣加深对命令-查询分离和重构代码等原则的理解，我强烈推荐 Pluralsight 的 Mark Seeman 的[封装和坚实课程](https://app.pluralsight.com/library/courses/encapsulation-solid/table-of-contents)。

您还可以查看我收集的 React 和 TypeScript 内容！

[![kamranayub image](img/9d88349209a0e5dda90018f3a7389079.png)](/kamranayub) [## 如何开始使用 React with Typescript

### kamran Ayub 3 月 5 日 2 分钟阅读

#react #typescript #guides](/kamranayub/how-to-get-started-using-react-with-typescript-21c)