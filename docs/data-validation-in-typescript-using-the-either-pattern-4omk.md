# 使用任一模式在 Typescript 中进行数据验证

> 原文：<https://dev.to/polyov_dev/data-validation-in-typescript-using-the-either-pattern-4omk>

今年夏天，我参加了一个实习项目，其中包括创建一个处理黑客马拉松的 CRUD(创建、读取、更新、销毁)应用程序。在这个项目中，我和我的团队发现，我们没有全面的解决方案或模式来验证进入应用程序的创建和更新操作的数据。

最后，我们的 API 方法总是包括检查字段的存在，然后根据该字段检查一些值，等等。我们没有使用 Typescript 强大的类型检查功能，而是在模型上频繁使用`any`和可选字段。这是一个烂摊子(实习项目可能就是如此)。

```
interface Hackathon {
  name: string;
  endDate?: number; // UNIX epoch timestamp 
  startDate?: number; 
  ...
}

validateHackathon(hackathon: any) : void {
    if (hackathon['endDate'] && hackathon['startDate']) {
        if (hackathon['endDate'] < 0) {
            throw new Error("End date cannot be negative!");
        }
        if (hackathon['startDate']) < 0) {
            throw new Error("Start date cannot be negative!");
        }
        if (hackathon['startDate'] > hackathon['endDate']) {
            throw new Error("Start date must be before end date!");
        }
    }
    // ... various property checks and data validation steps ...
}

async updateHackathon(hackathon: any) : void {
    validateHackathon(hackathon);
    // If the program gets to this step, then the object must have correct data and the correct type
    await this.repository.updateItem(hackathon as Hackathon);
} 
```

在我从事这个项目的同时，我一直在学习 Haskell，一种强大的纯函数式编程语言。因为这篇文章并不是要说服你去学习 Haskell，所以我将只介绍一种可以在该语言的基础库中找到的强大模式:`Either`。或者更确切的说，`Either a b`。我们将讨论如何将这种模式引入到 Typescript 中，以及如何通过一些设置和背景知识使数据验证变得更加简单。

## 什么是要么？

本质上，这两种类型都可以表示其他两种类型中的一种。在 Haskell 中，这个想法被写成`Either a b`，其中`a`和`b`代表另外两种类型。但是一次只能表示一种类型。所以顾名思义，在运行时，`Either a b`只能是`a`或者`b`，不能两者都是。`Either Int String`会不会*或者*是整数或者字符串。

为了确定在任何给定的时间内这两种形式中的哪一种，类型的两个选项将被包装在一个特殊的值中。在 Haskell 中，这些选项被称为`Left`和`Right`。所以一只`Either Int String`可以是一只`Left Int`或者一只`Right String`。一般来说，这种模式被称为*标记的*或*区别并集* ( [维基百科](https://en.wikipedia.org/wiki/Tagged_union))。通过使用“标记”或指示正在使用的类型的对象，这两个独立的类型被组合成一个类型。

在 Haskell 中，这两者的定义都采用一般代数数据类型的形式:

```
data Either a b = Left a | Right b 
```

这里，竖线`|`指的是逻辑 OR，这里，`Either a b`也可以是`Left a`或`Right b`。当我们用 Typescript 编写时，我们将重用这个语法。

`Either`的强大之处在于它在错误处理中的使用。按照惯例，`Left`类型是“错误”类型，`Right`类型是“值”类型。当一个`Either`值通过程序时，操作在正确的值上执行。如果出现错误，错误的信息可以“存储”在左边的类型中。然后程序将继续，检查是否存在错误，并传递错误信息，在此过程中不执行任何其他计算。

因此，可以编写一个操作序列，例如数据验证，使得每个验证步骤都可以抛出自己的错误，并且发现的第一个错误将通过操作序列传播，而不是从程序的正常逻辑中分支出来。

## 要么在打字稿中

从理论定义可以看出，这两种模式都非常强大。但是我们能用打字稿写它吗？是啊！幸运的是，只要我们编写一些其他方法来帮助 Typescript 编译器推断实际使用的是哪种标记类型，Typescript 就包含了对区分联合的[支持。所以我们用 Typescript 写`Either`。](https://www.typescriptlang.org/docs/handbook/advanced-types.html#discriminated-unions)

首先，我们想要定义具有共享(标记)属性(也称为“判别式”)的接口。我们还需要利用泛型，这样任何类型都可以包含在我们的 union 对象中。由于我们使用的是`Left`和`Right`，我们将把它们作为我们的接口名，并且我们将在每个接口中使用两个属性来创建联合的结构:`value`将保存对象的实际类型值，`tag`将纯粹引用正在使用的容器类型。

```
interface Left<A> {
  value: A;
  tag: 'left'
}

interface Right<B> {
  value: B;
  tag: 'right'
} 
```

(两个接口都可以使用`A`来引用泛型类型，但是看到同一个字母可能会让人混淆。)

既然我们有了自己独立的接口，我们需要声明一个类型别名，它将引用*`Left`或`Right` :* 

```
type Either<A,B> = Left<A> | Right<B>; 
```

如果我们只写了`Either<A>`，我们就不会得到我们想要的行为:任何一方的两边都必须持有相同的类型，而不是两种不同的类型。

最后，我们可以编写帮助器函数，Typescript 需要这些函数来将标记值转换为类型推断。

```
function isLeft<A>(val: any): val is Left<A> {
  if ((val as Left<A>).tag === 'left') return true;
  return false;
}

function isRight<B>(val: any): val is Right<B> {
  if ((val as Right<B>).tag === 'right') return true;
  return false;
} 
```

简单地说，这些函数将其传入值转换为一个`Left`或`Right`，然后检查标记字段的值。`val is Left<A>`奇怪的返回值是对编译器的注释，在即将到来的上下文中，`val`的类型是`Left<A>`。

最后，我们将为`Left`和`Right`类型编写一些构造函数。鉴于上面的接口定义告诉我们左值和右值可能是什么样子，我们可以编写一个类似构造函数的方法来显式地创建这些对象:

```
function Left<A>(val: A) : Left<A> {
  return { value: val, tag: 'left' };
}

function Right<B>(val: B) : Right<B> {
  return { value: val, tag: 'right' };
} 
```

当我们编写上面的接口时，我们本质上定义了一个叫做“左”和“右”的类型在这里，我们使用相同的名称编写函数，Typescript 可以理解，因为函数名和类型名是分开的。

## 这和黑客马拉松有什么关系？

让我们实际上把这些放在一起做一些数据验证！假设我们需要的关于验证过程中发生的错误的唯一信息是一个字符串。让我们在我们的方法签名中创建一个快速类型别名来说明这一点:

```
type MyError = string; 
```

超级简单。现在，我们可以从上面写`validateHackathon`方法，但是使用`Either` :

```
validateHackathon(h: Hackathon) : Either<MyError, Hackathon> {
  if (h.endDate < 0) {
    return Left<MyError>("End date cannot be negative!");
  }
  if (h.startDate < 0) {
    return Left<MyError>("Start date cannot be negative!");
  }
  if (h.startDate > h.endDate) {
    return Left<MyError>("Start date must be before end date!");
  }
  // etc
  return Right<Hackathon>(h);
} 
```

你可能会问自己，我们怎么能在一个地方向左返回，在另一个地方向右返回呢？这来自于我们对`Either`定义的逻辑 OR 方面。既可以是左类型，也可以是右类型，所以只要返回值是左类型或右类型，类型签名就成立。

此外，注意这里我们要求传入的值是类型`Hackathon`，而在上面的函数中它是一个`any`类型，我们在最后将其转换为`Hackathon`。清理验证的一部分是将传入数据的结构与我们可能对其值的任何限制分离开来。验证数据结构可以用 [JSON 模式和验证器](https://json-schema.org/)来完成。验证我们对输入数据值的限制是我们的`Either`方法要解决的问题。

所以，这个方法很有趣，但是它和我们之前的方法并没有什么不同。现在我们只有一个时髦的方法签名，我们使用这些左和右构造函数，而不是抛出一个错误或返回值。有什么特别的？

## 创建谓词函数

如果我们仔细观察现有的验证函数，我们可以看到它有一个重复的结构:使用 If 语句，我们检查传入值的一些属性。如果条件不成立，我们抛出相应的错误。对于不同的属性和它们的误差，我们一遍又一遍地这样做。

任何取值并返回真或假的函数都被称为[谓词](https://en.wikipedia.org/wiki/Predicate_(mathematical_logic))。使用这两种方法，我们可以编写一个函数，根据谓词计算某个对象，如果谓词没有通过，那么产生的`Either`将采用左边的错误形式。我们可以把这个方法叫做`predicateEither`。我们还将为谓词函数创建一个类型别名，这样我就不必在每个帮助器方法签名中重写这些谓词签名:

```
type Predicate<N> = (val: N) => boolean;

function predicateEither<A, B>(value: B, error: A, predicate: Predicate<B>) : Either<A, B> {
    if (!predicate(value)) return Left(error);
    return Right(value);
} 
```

例如，现在我们可以用一个谓词来验证负日期:

```
const StartDateMustBePositive = (h: Hackathon) => h.startDate > 0;

let badHackathon : Hackathon = {
  name: "Bad",
  startDate: -10,
  endDate: -10
};

let result = predicateEither(badHackathon, "Start Date must be positive!", StartDateMustBePositive);

// Result = Left "Start Date must be positive!"

let goodHackathon : Hackathon = {
  name: "Good",
  startDate: 10,
  endDate: -10
};

result = predicateEither(goodHackathon, "Start Date must be positive!", StartDateMustBePositive);

// Result = Right (goodHackathon) 
```

请注意，我们不需要在任何地方包含泛型类型指示符，因为 Typescript 可以为我们填补空白！

## 组合谓词

但是等等，你可能会说。“好的黑客马拉松”实际上并不好，它仍然有一个负的结束日期！

你是对的，所以我们应该为此写另一个谓词函数。但是我们如何把它和第一个谓词结合起来呢？我们不希望每次使用`predicateEither`时都检查结果值，因为那样我们还不如进行手动错误处理，并且我们将在程序中创建许多分支:

```
const EndDateMustBePositive = (h: Hackathon) => h.endDate > 0;

function validateHackathon(h: Hackathon) : Either<MyError, Hackathon> {
  let result = predicateEither(h, "Start Date must be positive!", StartDateMustBePositive);
  if (isLeft(result)) return result; // Branch!
  result = predicateEither(h, "End Date must be positive!", EndDateMustBePositive);
  if (isLeft(result)) return result; // Repetitive!
  return result;
} 
```

我最喜欢的编程原则之一是 DRY(不要重复自己)，我们在这里肯定违反了这一原则。所以让我们写一个最终的帮助函数，让这一切努力都值得。

这种方法叫做`firstLeft`。它接受一个初始值、一个谓词列表和一个错误列表。针对每个谓词测试该值，直到有一个谓词失败，在这种情况下，将返回相应的错误。如果没有谓词失败，将返回该值。

```
function firstLeft<A, B>(val: B, predicates: Predicate<B>[], errors: A[]) : Either<A, B> {
    for (let i = 0; i < predicates.length; i++) {
        let p = predicates[i];
        if (!p(val)) return Left(errors[i]);
    }
    return Right(val);
} 
```

有了这个结构，我们可以创建一个谓词及其错误的列表，并相信第一个发现的错误将是我们被提醒的错误:

```
let predicates = [ StartDateMustBePositive, EndDateMustBePositive ];
let messages = [ "Start Date must be positive!", "End Date must be positive!" ];

function validateHackathon(h: Hackathon) : Either<MyError, Hackathon> {
    return firstLeft(h, predicates, messages);
}

async updateHackathon(h: Hackathon) : void {
    let result = validateHackathon(h);
    if (isLeft(result)) {
        console.error(result.value);
        return;
    }
    await this.repository.updateItem(h);
} 
```

毒品。我们刚刚将重复的、分支的混乱转换成一行，并且我们已经确保，在出现验证错误的第一个迹象时，原来的逻辑不会继续。

## 一个用于验证的“规范”

我可以就此打住，但是我想稍微改变一下我们的`firstLeft`方法。将谓词和消息作为两个独立的数组感觉是错误的；如果有人添加了一个谓词，但忘记添加相应的错误消息，该怎么办？由于`indexOutOfBounds`问题，程序会突然中断正确的输入。

在这种情况下，我想利用元组，或者更确切地说，我们必须在 Java/Typescript 中使用元组来代替元组。如果我们使用元组样式的对象，我们可以有效地创建一个大的谓词列表及其相应的错误消息。这个大列表可以充当对象的“规范”:对象必须满足的任何属性都可以在列表中找到。

让我们制作一个小的“Pair”类型，并用它来创建这样一个规范:

```
interface Pair<A,B> {
    first: A;
    second: B;
}

function firstLeft<A, B>(val: B, predicatePairs: Pair<Predicate<B>, A>[]): Either<A, B> {
    for (let i = 0; i < predicatePairs.length; i++) {
        let p = predicatePairs[i].first;
        let e = predicatePairs[i].second;
        if (!p(val)) return Left(e);
    }
    return Right(val);
}

const HackathonSpec : Pair<Predicate<Hackathon>, MyError>[] = [
 { first: StartDateMustBePositive, second: "Start Date must be positive!" },
 { first: EndDateMustBePositive,   second: "End Date must be positive!" }
];

function validateHackathon(h: Hackathon) : Either<MyError, Hackathon> {
    return firstLeft(h, HackathonSpec);
} 
```

## 更复杂的谓词

当您使用简单谓词时，这种模式真的很酷，但是业务逻辑从来都不简单。我们如何将这种模式应用于更复杂的谓词，需要不止一个输入？

答案是，我们可以在谓词中编写任何类型的复杂逻辑，只要我们找到一种方法来确保它们接受一个输入并返回一个布尔值。例如，在我们的实习项目中，我们必须确保即将到来的黑客马拉松的日期不会与任何现有的黑客马拉松日期重叠。

为了测试这个断言，我们必须对照每个其他的黑客马拉松来检查即将到来的黑客马拉松。您可能认为这意味着我们的谓词必须有两个输入:`(incomingHackathon: Hackathon, existingHackathons: Hackathon[])`。但是我们可以使用闭包在谓词函数中引入现有的黑客马拉松:

```
class HackathonController {
    getAllHackathons(): Hackathon[];

    DatesMustNotOverlap = (h: Hackathon) => {
        return this.getAllHackathons()
                     .map<boolean>(v => v.endDate >= h.startDate 
                                     || v.startDate <= h.endDate )
                     .reduce((p, c) => p && c);
    };
    // etc
} 
```

## 总之

总的来说，以这种方式使用任何一种都可以创建一个强大的模式，使数据验证步骤变得更加清晰，错误消息也更加有用。还有很多其他的事情可以用任一个，Pairs，和有歧视的 unions 来做，希望以后能多探讨和讨论！

#### 给那些知道自己在说什么的人一个脚注

我应该说:我对 Haskell 及其强大的思想仍然非常陌生，比如单子、函子、应用程序和转换器。我仍在努力学习和完全理解这些想法。这两者都是一个有趣的概念，我发现通过在 Typescript 中实现，我可以更全面地理解它(毕竟，Javascript 是我学习的第一门语言)。

因为 Typescript 缺少函数式编程的一些功能强大的方面，而这些方面真正将这两种模式提升到了一个新的水平(尤其是部分函数应用程序)，所以这种实现远不如 Haskell 的强大！不过没关系。*