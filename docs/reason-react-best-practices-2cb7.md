# 推理(反应)最佳实践-第 1 部分

> 原文：<https://dev.to/fhammerschmidt/reason-react-best-practices-2cb7>

在使用 React 的 Reason 近 2 年后，我决定在维也纳的 ReasonML 会议( [@reasonvienna](https://twitter.com/reasonvienna) )上举办一场关于 Reason 和 ReasonReact 的最佳实践的讲座。鉴于这是我在技术会议上的第一次技术演讲，它进行得相当不错。同一天，我在 Twitter 上发布了这些幻灯片，但不幸的是，这些幻灯片并没有讲述完整的故事。所以我决定试一试，以更具描述性的形式放在这里。我也把它分开了一点，因为这个演讲花了大约一个小时，我喜欢让博客帖子简短扼要。

但是废话少说，让我们进入正题:

## 身份诡计

### 零成本 React.strings！

由于 Reason 是一种 100 %类型安全的语言，所以在 Reason 中编写 JSX 有一些特性，这使得初学者很难，尤其是当它们来自 JS 并用于将字符串直接写入`<div />`或任何其他允许儿童使用的 React 元素时。按理说，`React.string`方法在应用程序中任何想要呈现文本的地方都是需要的，所以为它创建一个单字母函数的绑定是有意义的。

你可以通过写
来实现

```
let s = React.string; 
```

Enter fullscreen mode Exit fullscreen mode

但是你可以利用编译器优化掉所有不必要的部分，通过使用`external`关键字:

```
/* Shorter replacement for React.string */
external s: string => React.element = "%identity"; 
```

Enter fullscreen mode Exit fullscreen mode

要比较这两种方法，请查看 ReasonML playground 中的这个例子: [ReasonML Try - 1](https://reasonml.github.io/en/try?rrjsx=true&reason=LYewJgrgNgpgBAMQJYCcDOAXOBeOBvAKDjlizRzgCUYBDAYwwDpMUkA7AcwG4Ci4BtAAIpaDRnRDAADiDYw2GALp9ScYDQDW8XAAoAlDgB8+PsWIAeMEgBuxvGh0AiZOgyO9AXzjmA9Fdt8HjxBvKCQsHAAyjASbGAUhMQwAB4YMChsNFBwaABcORisnEZUokwwsMDyWLiOAKRIYNVIGACejjzEfEIi9EwS0rLVysSq6loU+iWJZhb+dg6O0bFg7l6+84HBXEA) 。

正如您所看到的，在第一个例子中仍然有一个函数`s`被创建，而第二个函数却找不到了。这可能是一个非常非常小的运行时改进，但它很好地说明了 BuckleScript 编译器是如何工作的。如果你看一下来源，它实际上与来自[的`React.string`reason react](https://github.com/reasonml/reason-react/blob/v0.7.0/src/React.re#L5)是一回事。

无论哪种方式，你可能都应该把助手函数放在一个实用程序模块中，这样你就可以`open`在 react 元素中需要那些宝贵字符串的任何地方，就像这样:

```
open ReactUtils;

[@react.component]
let make = () => {
  <div>
    {s("Hello, dev.to!")}
  </div>
}; 
```

Enter fullscreen mode Exit fullscreen mode

我将该模块命名为`ReactUtils`，因为它专门用于处理 React 元素。

### 让你的代码库免于魔法！

有时类型系统过于严格，在异构软件系统中可能存在完全相同的类型，只是名称不同。使用`identity`技巧，你可以转换实际上相同的类型，例如`Js.Dict.t(string)`和`Js.Json.t` :

```
type apples = Js.Dict.t(string);
type oranges = Js.Json.t;

let (apples: apples) = Js.Dict.fromArray([|("taste", "sweet")|]);

let eatFruits = (fruits: Js.Json.t) => Js.log2("Eating fruits", fruits);

eatFruits(apples); 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们这里有苹果和橘子。两者都是水果，众所周知，但只有橙子实际上也被定义为`Js.Json.t`。然而，苹果被定义为`Js.Dict.t(string)`，这使得编译器抛出一个错误(参见 [ReasonML Try - 2](https://reasonml.github.io/en/try?rrjsx=true&reason=C4TwDgpgBAhmYBsIGcoF4oClkDoAiAlgMbA7AAUywATgQHYDmAlANwBQokUA9tTIynRZc2bnTLs2SYFHJxEKAFyx4SZEyHZ8xUgDNq3ALYBBanxDkA2gB9yAImAwqEOwBood5AHcIEYHaZrAF1WNik-KAgYYAAxagBXAmBUDHJ9ROTlLVFxYA00AD5hHARuBgAmewBRaPoGKHSk5DcGhKbQtijYtuS5VRRQoA) )。

编译这段代码最简单的方法是使用`Obj.magic`。它基本上关闭了类型检查器，让编译器做他的工作。

```
eatFruits(apples->Obj.magic); 
```

Enter fullscreen mode Exit fullscreen mode

( [ReasonML Try - 3](https://reasonml.github.io/en/try?rrjsx=true&reason=C4TwDgpgBAhmYBsIGcoF4oClkDoAiAlgMbA7AAUywATgQHYDmAlANwBQokUA9tTIynRZc2bnTLs2SYFHJxEKAFyx4SZEyHZ8xUgDNq3ALYBBanxDkA2gB9yAImAwqEOwBood5AHcIEYHaZrAF1WNik-KAgYYAAxagBXAmBUDHJ9ROTlLVFxYA00AD5hHARuBgAmewBRaPoGKHSk5DcGhKbQtijYtuS5VRQAWgKAeQAjACscQxgGYlCgA)

`Obj.magic`实际上也是通过使用恒等式技巧实现的:

```
external magic : 'a => 'b = "%identity"; 
```

Enter fullscreen mode Exit fullscreen mode

(参见[源代码](https://github.com/BuckleScript/ocaml/blob/698e60f3cd2f442f2013e79860ce2f7b0a9624cb/stdlib/obj.ml#L20))。

但是为这两种特定的类型编写一个转换函数更符合习惯(而且风险更小):

```
type fruits = Js.Json.t;
external applesToFruits: apples => fruits = "%identity";

eatFruits(apples->applesToFruits); 
```

Enter fullscreen mode Exit fullscreen mode

这让你的代码可以编译，并且仍然确保只有一个不同的类型被函数使用，而不是所有的*。有时使用`Obj.magic`仍然是有意义的，尤其是当你只想创建一个快速原型的时候(见 [ReasonML Try - 4](https://reasonml.github.io/en/try?rrjsx=true&reason=C4TwDgpgBAhmYBsIGcoF4oClkDoAiAlgMbA7AAUywATgQHYDmAlANwBQokUA9tTIynRZc2bnTLs2SYFHJxEKAFyx4SZEyHZ8xUgDNq3ALYBBanxDkA2gB9yAImAwqEOwBood5AHcIEYHaZrAF1WNik-KAgYYAAxagBXAmBUDHJ9ROTlLVFxYA00AD5hHARuBgAmewBRaPoGKHSk5DcGhKbQjnBoRuTNEWQxCTYIAA9gCGo6GAQVBWQAFW44jORleTV0Ip6UjwBSAgATCDpgJJA7SSjYtuS5VRQAWgL1lEXl9vYgA) )。*

 *## 管道

### 像马里奥一样用管子！

正如许多(函数式)语言一样，Reason 也为我们提供了一个特殊的管道操作符(`->`)，它本质上是将代码翻转过来，例如`eatFruits(apple)`变成了`apple->eatFruits`。起初，我很难阅读和理解更长的管道链，但在使用了几天后，我习惯了。现在它们对我来说是最不可或缺的特征之一。

*   使您不必为中间变量寻找名称。
*   保持代码整洁。
*   对`Belt` (BuckleScript 的标准库)及其`Option`模块特别有用，你会经常遇到它们，因为我们在 Reason land 中没有`null`或`undefined`。
*   当将管道优先(`->`)与`Array.map`一起使用时，它使代码看起来非常类似于 JS，例如:`[|1, 2, 3|]->Array.map(x => x * 2)`在普通 Js 中是`[1, 2, 3].map(x => x * 2)`。

但是只要比较下面的两个例子，第一个没有使用管道:

```
let vehicle = Option.flatMap(member.optionalId, id => vehicles->Map.String.get(id));
let vehicleName = Option.mapWithDefault(vehicle, "–", vehicle => vehicle.name);
s(vehicleName); 
```

Enter fullscreen mode Exit fullscreen mode

对比

```
member.optionalId
  ->Option.flatMap(id => vehicles->Map.String.get(id))
  ->Option.mapWithDefault("–", vehicle => vehicle.name)
  ->s 
```

Enter fullscreen mode Exit fullscreen mode

### 管道参数位置偏执狂！

我知道在最后一批人(主要是 native Reason 和 OCaml 开发人员)和第一批人(BuckleScript 开发人员)之间正在进行一些斗争，但是在 BuckleScript 中，因为您将 JS 作为目标语言，所以管道优先是一种方法。这种方式的类型推断效果也更好。

如果你真的想通过管道进入一个不符合 BS 习惯的函数(即位置参数不是第一个)，你可以使用`_`字符替换管道参数应该进入的位置:

```
"ReasonReact"->Js.String.includes("Reason", _); 
```

Enter fullscreen mode Exit fullscreen mode

而是最好使用管道优先版本的函数，只要它存在:

```
"ReasonReact"->Js.String2.includes("Reason"); 
```

Enter fullscreen mode Exit fullscreen mode

如前所述。

## 标签救场！

当你有一个类似`String.includes`的函数，其中多个参数具有相同的类型时，直接标注会好很多，否则你不会知道哪个参数是源字符串，哪个是搜索字符串:
[![string.includes](img/b83ac7e7dd71bc825dff09b1f40e2ec5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZAcDaMGx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7v33vi0zr2xmoz9j5ira.png)

更糟糕的是，如果您使用了错误的管道(`|>`)，并且不确定它采用了哪个参数，那么您很容易就会混淆。编译器不能把你从那种情况中拯救出来，因为类型是完全正确的。

这里有一个用标签绑定的`String.includes`函数，防止您猜测位置参数是哪个:

```
module BetterString = {
  [@bs.send] external includes : (string, ~searchString: string) => bool = "includes";
};

"ReasonReact"->BetterString.includes(~searchString="Reason"); 
```

Enter fullscreen mode Exit fullscreen mode

( [ReasonML Try - 5](https://reasonml.github.io/en/try?rrjsx=true&reason=LYewJgrgNgpgBAIRgF2TATgZWeglgOwHM4BeOAbwCg44BtAAQCMBnAOmZnzAF04YAPNOnwBDKHAIBjKBDAxmcAFxwAFMxwFCAGjgA-DiPSSAFtjxFl684QCUpAHxxGIEOLIAiKTLnN3AbkoAXwDKdwAlGBFmEHwIkUlkdwBaeyRUDDNNVi9ZeRVwyOj8dxsUgCk2KBBCPyA) )

公平地说，我们现在需要多输入一点，但是我们获得了一些不确定性，并且不需要检查 MDN 来确定。此外，当您忽略警告或停用相应的编译器警告(第 6 条)时，您仍然可以调用函数而不标记参数。不过，我还是建议你不要那样做。

*原因(反应)最佳实践*的第一部分到此结束，我们的下一个主题将是关于 [BuckleScript 的编译器配置和带](https://dev.to/fhammerschmidt/reason-react-best-practices-part-2-2opc)。*