# 打字稿程序员的 5 条戒律

> 原文：<https://dev.to/miloszpp/5-commandments-for-typescript-programmers-2jp1>

越来越多的项目和团队采用 TypeScript。然而，仅仅使用 TypeScript 和充分利用它是有很大区别的。

我向您展示这份高层次的 TypeScript 最佳实践列表，它将帮助您尽可能充分地利用 TypeScript。

**这篇文章还有俄文版:[5заповедейtypescript-разработчика](https://habr.com/ru/post/461565)T3(作者[瓦迪姆·别洛鲁索夫](https://dev.to/bevalorous))。**

## 不说谎

**类型是一个合同。**什么意思？当你实现一个函数时，它的类型就是对其他开发者的承诺(或者对你未来的自己！)当他们调用它时，它将返回一种特定的值。

在下面的例子中，`getUser`的类型承诺它将返回一个对象，该对象将**总是**具有两个属性:`name`和`age`。

```
interface User {
  name: string;
  age: number;
}

function getUser(id: number): User { /* ... */ } 
```

TypeScript 是一种非常灵活的语言。为了使它更容易被采用，它充满了妥协。例如，它允许你像这样实现`getUser`:

```
function getUser(id: number): User {
  return { age: 12 } as User;
} 
```

不要这样！这是骗人的。这样做，就是欺骗了其他开发者(他们会在自己的函数中使用这个函数)。他们期望由`getUser`返回的对象总是有一些`name`。但事实并非如此！那么，当你的队友写`getUser(1).name.toString()`时会发生什么？你很了解它...

当然，这个谎言看起来非常明显。然而，当处理一个巨大的代码库时，您会经常发现自己处于这样一种情况:您想要返回(或传递)*的值几乎*匹配预期的类型。**找出类型不匹配的原因需要时间和精力**而且你很着急...所以你决定。

然而，这样做，你**违反了神圣的契约**。花时间找出类型不匹配的原因总比进行转换要好。这很可能是一些运行时的错误潜伏在表面之下。

不要说谎。尊重你的合同。

## 精确

**类型是文档。当你记录一个函数时，你不想尽可能多的传达信息吗？** 

```
// Returns an object
function getUser(id) { /* ... */ }

// Returns an object with two properies: name and age
function getUser(id) { /* ... */ }

// If id is a number and a user with given id exists,
// returns an object with two properies: name and age.
// Otherwise, returns undefined.
function getUser(id) { /* ... */ } 
```

你更喜欢对`getUser`的哪个评论？你对它返回的内容了解得越多越好。例如，知道它可能返回`undefined`，您可以编写一个`if`语句，在访问它的属性之前检查它返回的值是否被定义。

类型也是一样的。一个类型越精确，它传达的信息就越多。

```
function getUserType(id: number): string { /* ... */ }

function getUserType(id: number): 'standard' | 'premium' | 'admin' { /* ... */ } 
```

第二个版本的`getUserType`信息量更大，因此它将呼叫者置于一个更好的位置。如果你确定一个值是**(契约，记得吗？)三个字符串之一，而不是它可以是任何字符串。首先，您肯定知道该值不是空字符串。**

 **让我们看一个更现实的例子。类型表示从后端获取一些数据的组件的状态。这种类型精确吗？

```
interface State {
  isLoading: boolean;
  data?: string[];
  errorMessage?: string;
} 
```

这种类型的使用者必须处理一些不太可能的属性值组合。例如，不可能同时定义`data`和`errorMessage`(数据提取可能成功，也可能导致错误)。

在有区别的联合类型的帮助下，我们可以使类型更加精确:

```
type State =
   | { status: 'loading' }
   | { status: 'successful', data: string[] }
   | { status: 'failed', errorMessage: string }; 
```

现在，这种类型的消费者拥有更多的信息。它们不需要处理属性值的非法组合。

**精确。用你的字体传达尽可能多的信息。**

## 从类型开始

因为类型既是契约又是文档，所以它们对于**设计**你的函数(或方法)非常有用。

互联网上有很多文章建议软件工程师在编写代码之前先思考。我完全同意这种做法。直接进入代码很有诱惑力，但是它经常导致一些糟糕的决定。花一些时间考虑实现总是有回报的。

类型在这个过程中非常有用。*思考*可能会导致写下您的解决方案中涉及的函数的类型签名。这很棒，因为它让你专注于*你的功能做了什么*，而不是*它们是如何实现的*。

React JS 有一个高阶组件的概念。它们是以某种方式增加给定组件的功能。例如，您可以创建一个`withLoadingIndicator`高阶组件，将一个加载指示器添加到一个现有的组件中。

让我们为这个函数编写类型签名。它接受一个组件并返回一个组件。我们可以用 React 的`ComponentType`来表示一个组件。

`ComponentType`是由组件的属性类型参数化的泛型类型。`withLoadingIndicator`获取一个组件并返回一个新组件，该组件或者显示原始组件，或者显示加载指示器。这个决定是基于一个新的布尔属性`isLoading`的值做出的。因此，生成的组件应该要求与原始组件相同的属性加上新的属性。

先敲定类型吧。`withLoadingIndicator`接受一个类型为`ComponentType<P>`的组件，其中`P`表示属性的类型。它返回一个具有类型`P & { isLoading: boolean }`的增强属性的组件。

```
const withLoadingIndicator = <P>(Component: ComponentType<P>) 
    : ComponentType<P & { isLoading: boolean }> =>
        ({ isLoading, ...props }) => { /* ... */ } 
```

弄清楚这个函数的类型迫使我们考虑它的输入和输出。换句话说，是它让我们*设计了它*。编写实现现在是小菜一碟。

**从类型开始。让类型迫使你在实现之前进行设计。**

## 拥抱严格

前三点需要你非常注意类型。幸运的是，在这项任务中，您并不孤单——TypeScript 编译器通常会让您知道您的类型何时出错或者何时不够精确。

您可以通过启用`--strict`编译器标志使编译器更加有用。它是一个元标志，启用所有严格的类型检查选项:`--noImplicitAny`、`--noImplicitThis`、`--alwaysStrict`、`--strictBindCallApply`、`--strictNullChecks`、`--strictFunctionTypes`和`--strictPropertyInitialization`。

他们是做什么的？通常，启用它们会导致更多的 TypeScript 编译器错误。这很好！更多的编译器错误意味着更多的编译器帮助。

让我们看看启用`--strictNullChecks`如何帮助你识别谎言。

```
function getUser(id: number): User {
    if (id >= 0) {
        return { name: 'John', age: 12 };
    } else {
        return undefined;
    }
} 
```

`getUser`的类型承诺它将总是返回一个`User`。然而，正如您在实现中看到的，它也可以返回一个`undefined`值！

幸运的是，启用`--strictNullChecks`会导致编译器错误:

```
Type 'undefined' is not assignable to type 'User'. 
```

TypeScript 编译器检测到谎言。说实话就能摆脱错误:

```
function getUser(id: number): User | undefined { /* ... */ } 
```

**拥抱类型检查的严格性。让编译器监视您的步骤。**

## 保持最新

TypeScript 语言正以非常快的速度发展。每两个月有一个新版本。每个版本都带来了显著的语言改进和新特性。

通常情况下，新的语言特性允许更精确的类型和更严格的类型检查。

例如，2.0 版本引入了有区别的联合类型(我在*中提到过，确切地说是*)。

3.2 版本引入了`--strictBindCallApply`编译器选项，可以正确输入`bind`、`call`和`apply`函数。

[3.4 版本改进了高阶函数中的类型推断](https://codewithstyle.info/TypeScript-3-4-hidden-gem-propagated-generic-type-arguments/)，使得用函数风格编写代码时更容易使用精确类型。

我在这里的观点是，熟悉最新版本的 TypeScript 中引入的语言特性确实是值得的。他们通常可以帮助你坚持这份清单中的其他四条戒律。

一个很好的起点是官方的打字路线图。定期查看微软开发博客的 [TypeScript 部分也是一个好主意，因为所有的发布公告都在那里发布。](https://devblogs.microsoft.com/typescript/)

与新的语言功能保持同步，并使其为您服务。

## 总结

我希望这个列表对你有用。像生活中的任何事情一样，这些戒律不应该被盲目地遵守。然而，我坚信这些规则会让你成为更好的 TypeScript 程序员。

我很想在评论区听到你的想法。

## 想了解更多？

你喜欢这篇打字稿吗？我打赌你也会喜欢我的课程！

[高级类型脚本主类](https://www.educative.io/courses/advanced-typescript-masterclass)**