# 使缺陷成为不可能

> 原文：<https://dev.to/integerman/making-defects-impossible-16c>

软件错误是糟糕的，但是相同类型的重复错误会令人沮丧。有多少次我们看到错误消息包含类似“对象引用没有设置为对象的实例”这样的字符串？作为软件工程师，我们可以在发现一次性事件时与之斗争，或者我们可以在识别缺陷时积极地寻求消除缺陷的常见原因。

每当你看到一个缺陷时，问问你自己这个缺陷是如何存在的，它是如何被发现的，以及你能做些什么来消除将来类似缺陷的可能性或者使它们无法隐藏。

当然，我们不能消除所有类型的问题，但我们可以在设计或语言层面战略性地解决的问题类型每年都在增加。

本文是从. NET 和 JavaScript 开发经理的角度撰写的，但是这些技术也可以更广泛地应用于其他语言。

# 在编译时识别运行时错误

从一个简单而显而易见的问题开始，编译语言让您能够在编译时捕捉可能的运行时错误。在 JavaScript 中，你可以很容易地写出这样一个函数:

```
function myMethod(a, b, c) {} 
```

Enter fullscreen mode Exit fullscreen mode

并尝试通过:
调用它

```
mymethod(1, 2, 3); 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 可以很好地解析，但是当找不到`mymethod`时会创建一个运行时异常。

TypeScript 将在编译时捕捉到这一点(更确切地说，是 transpilation ),防止您犯这一错误。此外，TypeScript 通过类似
的语法为您提供静态类型检查

```
public myMethod(a: number, b: number, c: number): void {} 
```

Enter fullscreen mode Exit fullscreen mode

这将捕捉你试图调用它的问题，就像

```
myMethod(1, 2, '3'); 
```

Enter fullscreen mode Exit fullscreen mode

当然，这带走了动态类型语言的一些优势，但是您可以在 TypeScript 中混合强类型定义和更通用的 JavaScript。此外，即使静态类型的编译语言也有动态语言能力，例如. NET 中的关键字`dynamic`。

# 用不变性保证有效性

在编程中，不变性指的是对象的状态不可改变。这种限制可能有一些性能上的好处，但是它提供的质量上的好处有时会被忽略。

拿着。NET `DateTime`对象为例。如果试图创建一个新的代表 1 月 35 日或其他无效日期的`DateTime`实例，构造函数将抛出一个异常。`DateTime`对象的设计方式是，如果你有一个实例，你知道它代表一个有效的日期，不需要对它做任何验证。

这样做的代价是，您不能将代表 1 月 28 日的实例修改为 35 日，因为它代表的日期是不可变的。例如，如果您想前进一天，您可以调用一个方法将一个`TimeSpan`添加到`DateTime`实例中，这将创建一个新的`DateTime`实例，该实例也处于良好状态(根据需要前进月份和年份)。

通过在您自己的类中采用这种技术，您可以为您的代码提供同样的质量优势。这是 F#等函数式语言通常支持的方法。

ImmutableJS 是一个非常著名的库，它在 JavaScript 中提供了不变性。

# 将验证烘焙成具有区别性联合的类型

F#和 TypeScript 都有一个被称为有区别的并集的概念。区别并集本质上是一个“或”类型的概念，即某事物是许多不同可能性中的一种。

打字稿中的经典例子如下:

```
Type User = AnonymousUser | AuthenticatedUser; 
```

Enter fullscreen mode Exit fullscreen mode

这允许您将返回类型、属性和参数声明为用户，这意味着它们可以是`AnonymousUser`或`AuthenticatedUser`。如果你有一些逻辑明确需要一个`AuthenticatedUser`，你可以调用一个带有类似于`authenticate(user: AnonymousUser): AuthenticatedUser`签名的方法将用户转换成一个`AuthenticatedUser`，然后要求某些方法接受一个`AuthenticatedUser`实例。这将验证加入到你的输入系统中。

这种方法的缺点是，几乎相同的类型会激增，需要为类型转换维护更多的代码。
在。NET 生态系统中，你可以使用 F#的区别性联合特性支持或者使用像的[one 这样的库来引入使用。NET 泛型语法。](https://github.com/mcintyre321/OneOf)

# 空引用异常

问一问. NET 开发商店中的几乎任何人(或者可能是他们的客户),他们都会看到可怕的“对象引用没有设置为对象的实例”的错误消息。

这是面向对象语言中的一个常见问题。通过定义引用变量，可以将引用设置为 null。
举个例子:

```
var myObject = someList.FirstOrDefault(o => o.Id == 42); 
```

Enter fullscreen mode Exit fullscreen mode

如果 Id 属性为 42 的对象在`someList`中，`myObject`现在将持有对它的引用，调用`myObject.DoSomething();`将起作用，但是如果 Id 为 42 的对象不存在于`someList`中，那么`myObject`将为空，并且您不能在空实例上调用方法，因此抛出空引用异常。

函数式编程语言通过[选项](https://dev.to/integerman/eliminating-nulls-in-c-with-functional-programming-iaa)的概念解决了这个问题。选项可以是 Some 和 None，Some 表示非空实体，None 表示空实体。

那么，这和面向对象语言中的标准引用，甚至是带有`HasValue`和`Value`方法的可空类型有什么区别呢？关键的区别在于你可以这样做:

```
Option<MyClass> myObject = FindInCollection(myList, 42);

int i = myObject.Some(val => val.MyIntegerProperty)
                .None(-1); 
```

Enter fullscreen mode Exit fullscreen mode

这使得与 null 值的交互变得显式，并迫使开发人员考虑 null 和非 null 场景。

上面的示例使用了。函数式编程的 NET [Language-Ext](https://github.com/louthy/language-ext) 库。在 TypeScript 中，你可以使用 [fp-ts](https://github.com/gcanti/fp-ts) 库，它提供了一组简单的函数式编程结构，包括选项。更多细节请看[我写的关于语言扩展选项的文章](https://dev.to/integerman/eliminating-nulls-in-c-with-functional-programming-iaa)。

[![integerman](img/c4617a5228f5ea62d27f47eb980b349d.png)](/integerman) [## 用函数式编程消除 C#中的空值

### matt Eland Sep 12 ' 194min read

#functional #csharp #dotnet #codequality](/integerman/eliminating-nulls-in-c-with-functional-programming-iaa)

* * *

最终，有许多方法可以解决常见的编程问题。这个列表仅仅触及了表面，我可以写另一篇文章，完全关于反应式编程和它可以解决的问题，但是希望这给你一个冰山一角，让你深入了解通过小心地应用工具、语言和库可以消除的问题类型。

请记住，这些技术中有许多在可读性或其他方面(特别是那些与函数式编程相关的方面)有所折衷，因此选择使用它们不应该是自动的，而是基于团队成员的技能水平和熟悉程度、代码库的状态以及您试图解决的问题类型的性质做出的谨慎决定。