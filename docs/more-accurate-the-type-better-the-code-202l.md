# 类型越准确，代码越好

> 原文：<https://dev.to/macsikora/more-accurate-the-type-better-the-code-202l>

想象一下，有人给你许多装在黑盒子里的未知物体，一个接一个。除非你问正确的问题，否则你不会知道盒子里有什么。至于橙子，你会问它是不是水果，它是不是橙色的。如果两个答案都是真的，那么里面可能是一个橙子。验证完物品后，你把它交给下一个人，他和你在同一个黑匣子里。下一个人需要再次找出这个物体，因为仍然没有关于它的信息，只有同一个黑盒。

[![](img/11b7fe5859c7fb8f414ef3569832a376.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cj2PBFBq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x5q0gflluwpxymnjzew2.png)

这正是函数在动态类型语言(如 JavaScript)中处理数据结构的方式。在你设置条件之前，它可以是任何东西。即使你问了，像物体属性这样的细节仍然是未知的。这就是为什么，在普通的 JS 中，每个地方都有大量的防御性检查，因为契约仍然是未知的，即使一些其他函数以前检查过。

### 知道得越少，问得越多

在现实生活中，我们需要检查物体，以了解可以用它做什么，我们使用我们人类的记忆和大脑专门识别熟悉的东西。你会在不知道也不知道它是什么的情况下把它抓在手里吗？这将是非常危险的，因为它可能是例如一把刀。

同样的知识需求也适用于编程。宽泛的类型，或者没有类型，给出的问题多于答案。所以如果你有很多问题，代码每次都需要问他们。而要求意味着——条件。你将如何处理这种宽泛而没有框架的字体:

```
interface Something {
  type: string;
  maxSpeed?: number;
  cookingTime?: number;
  wheelSize?: number;
  name?: string;
  lastname?: string;
  carModel?: string;
  age?: number;
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这将只是一个噩梦，即使在代码中，你会知道，你目前正在处理某辆车，你仍然可以询问这辆车`cookingTime`或`lastname`:)。上面的定义与好的类型定义正好相反——包含许多可选字段。另一件事是，任何人都不应该创建这样的多态结构。并且对代码的影响不是中性的，在每个地方都会有大量的条件，并且这些条件中的大多数会在它们没有意义的情况下完成。

## 真正的宽广型

让我们切换到一些真实的例子，我将把域改变成心爱的服务器响应结构，每个人在某个时候都需要使用它。我将假设我们负责与服务器通信的中间件以这样的方式对响应建模:

```
interface ServerResponse {
  code: number;
  content?: Content;
  error?: Error;
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，我们有，我可以说是很好的类型，至少比前一个更好。但我们还知道更多的东西，特定的响应代码对其他领域有特定的影响。这些关系就是:

*   对于- 500 和 400 这样的错误代码，有错误字段，但没有内容
*   对于 200 个代码，有内容，但没有错误
*   对于 404，没有内容也没有错误

然后，该类型具有隐藏的依赖关系，可以表示不可能的形状。属性`code`与属性`content`和`error`之间存在隐藏的依赖关系。

```
const resp = getResponse()
if (resp.code === 500) {
  console.log(resp.content && resp.content.text); // there never can be the content property
} 
```

Enter fullscreen mode Exit fullscreen mode

从类型的角度来看，这个条件是一个有效的问题，因为类型并没有说明字段关系，但实际上它不可能发生。此外，即使你知道总是存在错误字段，也总是需要进行防御性检查，因为类型并不代表这一点:

```
const resp = getRespomse()
if (resp.code === 500) {
  console.log(resp.error && resp.error.text); // the error property will be there always
} 
```

Enter fullscreen mode Exit fullscreen mode

## 类型过于宽泛

那怎么办。你可以只写代码，通过接触你自己的人类记忆或某种文档来避免这种事情，这些很快就会过时。换句话说，这些规则将作为这个项目的部落知识保留下来，偶尔有人会问——为什么 404 没有错误属性集，为什么有人检查错误响应中是否存在`content`。

<figure>

[![Project Shaman will help you :)](img/0ce64c60ccd8982bc73b9b6f5f00164c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9-zgBnl---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6lo32ko5oxizevxi5j5t.png)

<figcaption>Project Shaman will help you :)</figcaption>

</figure>

或者取而代之，您可以在类型中适当地建模这些关系。好消息是——在 TypeScript 中，你可以很好地做到这一点。

## 把知识转化为类型

让我们试着以正确的、狭隘的方式来形成类型。出于示例的目的，我将简化并说明服务器只能发送 500、400、404 和 200 个 http 代码。然后我可以提取以下类型:

```
interface SuccessResponse {
  code: 200;
  content: Content;
}

interface ErrorResponse {
  code: 400 | 500;
  error: Error;
}

interface NotFoundResponse {
  code: 404;
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我有三个不相关的类型。但是响应可以是或*成功*或*错误*或*未发现*。而正是我要做的，我将加入他们的联盟:

```
type ServerResponse = SuccessResponse | ErrorResponse | NotFoundResponse 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。是的，这就是全部。现在，代码和其他属性之间的所有关系都在类型中。没办法用`ErrorResponse`里的`content`或者`SuccessResponse`里的`error`，或者`NotFoundResponse`里的任何一个。如果我试图创建无效的对象，编译器会尖叫。此外,`code`字段也从宽泛的数字类型缩小为仅有的几个特定的可能性。

此外，在检查状态`code`后，TypeScript 会自动缩小类型的范围。所以如果你检查:

```
if (response.code === 500) {
  // here only `error` property is accessible
  console.log(response.error.text)
}

if (response.code === 200) {
  // here only `content` property is accessible
  console.log(response.content.text)
}

if (response.code === 404) {
  // here no additional properties are available
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，这些条件不需要直接使用。函数形式的额外抽象使用起来会方便得多

```
// declaration of the type guard function
const isErrorResponse = (response: Response): response is ErrorResponse => response.code === 500 || response.code === 400;

// using
if (isErrorResponse(resp)) {
  // in this scope resp is type of ErrorResponse
} 
```

Enter fullscreen mode Exit fullscreen mode

## 类型越准确，代码越好

我所做的是缩小类型，这正是你在使用静态类型语言时应该做的。由于类型是文档和代码指南，使它们准确符合您的利益。我在这里描述模式有一个名字——区别并集或标记并集。查看官方的 TS 文档。下次见！