# 类型脚本中的安全函数 IO:简介

> 原文：<https://dev.to/peterszerzo/safe-functional-io-in-typescript-an-introduction-1kmi>

这些天来，我大部分时间都在用 Elm 编程，享受着类型安全 IO 和保证没有运行时异常的丰富类型系统。然后，就在上周，一个相当复杂的 TypeScript 项目落到了我的头上。怀着兴奋和乐观的心情，我花了一些时间使用可爱的 [io-ts](https://github.com/gcanti/io-ts) 和 [fp-ts](https://github.com/gcanti/fp-ts) 包在这个新的地形上提供同样的保证。

本系列总结了这一学习之旅，在 TypeScript 中命名为*安全功能 IO。它以我以前的打字经历中的一些棘手问题开始，希望能有所收获。*

## 安全 IO 的需要

TypeScript 的类型系统已经变得如此之好，以至于在 v3.1 和更高版本中，我可以轻松地对 3000 loc React 应用程序进行外科手术式的修改，只需要依靠编译器和几十个单元测试。然而，这种好处只有在进入应用程序的数据按照我假设的方式运行时才有效:

```
interface Fruit {
  name: string;
  sweetness: string;
}

fetch("https://some.endpoint/fruit.json")
  .then(res => res.json())
  .then((fruit: Fruit) => {
    // do something with `fruit`
    // although I'm a bit worried 😨
  }) 
```

Enter fullscreen mode Exit fullscreen mode

保证`fruit`从服务器返回时是正确的形状是很棘手的，特别是因为它很容易信任后端团队的文档，像上面的代码片段那样注释 UI 代码，然后继续前进。

但是接下来，不可避免地会出现沟通不畅、部署版本不匹配、不可预测的缓存，由此产生的 bug 做了 bug 最擅长的事情:猛烈攻击并提供一种消息/堆栈跟踪，这对于缩小其位置没有什么帮助。

对我来说不是，对下一个产品的用户来说也不是。

## 输入`io-ts`

这个聪明的包公开了一种语言来定义上面的`Fruit`接口，它不仅呈现了一个静态类型，还呈现了一个非常优雅的运行时验证器。对于我们的例子，它看起来像这样:

```
import * as t from "io-ts";

// Validator object, called `codec`
const Fruit = t.type({
  name: t.string,
  sweetness: t.string
});

// Static type, constructed from the codec
type Fruit = t.TypeOf<typeof Fruit>; 
```

Enter fullscreen mode Exit fullscreen mode

这里有一点高级的 TypeScript 魔法，但是现在，知道我们有一个静态的`Fruit`类型就足够了，它的行为与之前的简单接口相同。有了`Fruit`对象，我们可以进行验证:

```
// Instead of the optimistic `Fruit`, we can now properly annotate as `unknown`
const someValue: unknown = {
  name: "apple",
  sweetness: "somewhat"
};

const validatedApple = Fruit.decode(someValue);
console.log(validatedApple); 
```

Enter fullscreen mode Exit fullscreen mode

控制台里出来的是这个:

```
{  "_tag":  "Right",  "right":  {  "name":  "apple",  "sweetness":  "somewhat"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

看起来这个物体试图告诉我，有些尴尬并且重复了很多次，有些事情是对的。但是我们真的被期望与这个一起工作吗？

是的，我们有，但是有很多优雅的帮助，而且绝对没有支票。

## 输入`fp-ts`

`io-ts`在内部和公共 API 中使用通用函数式编程工具包`fp-ts`的数据结构。使用这个包，上面的对象可以这样构造:

```
import * as either from "fp-ts/lib/either";

const validationFromBefore = either.right({
  name: "apple",
  sweetness: "somewhat"
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们对它进行注释，它会是这样的:

```
either.Either<t.Errors, Fruit> 
```

Enter fullscreen mode Exit fullscreen mode

表示一个类型可以有两条路可走:左边，由一个给定的类型参数化，右边，由另一个给定的类型参数化。要么是一个装着文档的文件夹，要么是一个装着苹果的篮子:总有一个容器，放心那个文件夹里不会有任何压扁的苹果。

更具体地说，左边通常包含一些错误(在我们的例子中是`t.Errors`)，而右边包含一些成功操作产生的有效负载(`Fruit`)。在 Elm 这样更友好的语言中，左-右三元组被称为 result-err-ok。

> `Either`在 TypeScript 中使用[区分联合](https://www.typescriptlang.org/docs/handbook/advanced-types.html#discriminated-unions)，这样编译器可以帮助我们正确使用这些容器和值。

### 与`Either`一起工作

正如我之前承诺的，`fp-ts`公开了一些方便的函数来轻松地使用`Either`值。为了将解码后的结果转换成友好的字符串以显示在 UI 中，我们可以使用一个单独的`either.fold`函数，该函数还带有两个函数:

*   `onLeft`:将误差转化为字符串的函数
*   `onRight`:将解码成功的水果变成字符串的函数

本质上，编译器只是强迫我们在与程序交互之前处理成功和错误的情况。在我们的例子中，它看起来像这样:

```
import * as t from "io-ts/lib/either";
import * as either from "fp-ts/lib/either";

// Decode an arbitrary value
const validation = Fruit.decode({
  name: "apple",
  sweetness: "somewhat"
});

const result: string = either.fold(
  (errors: t.Errors) => "Something went wrong",
  (fruit: Fruit) => `${fruit.name} ~ ${fruit.sweetness} sweet`
)(validation);

console.log(result);
// Logs "apple ~ somewhat sweet" 
```

Enter fullscreen mode Exit fullscreen mode

如果我们将`sweetness`字段中的值替换为 65(合理地假设甜度应该在 0-100 的范围内)，验证将失败，您将得到`"Something went wrong"`,正如预期的那样。

> 我建立了一个包含这些代码的代码沙箱。您可以随意在那里进行验证。它多少有些变化，可能会包含各种其他的好东西，但是这样不是更有趣吗🤓。

## 结论

在这篇文章中，我讲述了如何使用`io-ts`包确保 TypeScript 中的随机值符合静态接口，以及如何使用`fp-ts`处理最终的安全值。

接下来，我将设置一个小应用程序，它与模拟服务器对话并处理所有错误。`fp-ts`中会有一些状态建模，有更多的功能性招数可以享受。直到那时！