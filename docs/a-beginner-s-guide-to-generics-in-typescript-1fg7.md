# Typescript 中泛型的初学者指南

> 原文：<https://dev.to/bmcmahen/a-beginner-s-guide-to-generics-in-typescript-1fg7>

不能完全理解 typescript 中的泛型。在我看来，解释泛型的最好方法是类比。

考虑像 UPS 或 Puralator 这样的快递服务，在世界各地递送包裹。快递服务不需要知道包裹的确切性质——他们不需要知道它是玩具、书、衣服等等。，-但是期望是接收方将接收发送的内容。送一个玩具，就会收到一个玩具。快递员负责递送包裹，但不需要知道它所递送的内容的确切类型。

在这个类比中，快递服务是我们的职能，包裹是我们的通用。让我们用一些代码来阐明这个类比。这个例子(没有使用泛型)展示了一个 courier 函数，它接收一个字符串作为参数，并返回一个字符串。我们的返回值`delivery`被正确地输入为字符串。

```
function courier(package: string): string {
  return package
}

// delivery is typed as a string
const delivery = courier('book') 
```

但是大概我们希望我们的信使函数能够处理的不仅仅是字符串。我们应该能够处理数字、物体或任何东西…

我们可能会尝试使用`any`类型来实现这一点。

```
function courier(package: any): any {
  return package
}

// delivery is typed as any
const delivery = courier('book') 
```

这是可行的，但是现在我们的`delivery`值被输入为`any`。但是函数的调用者——发送包的人——不应该知道返回的包的类型吗？换句话说，如果我们的`delivery`值保留最初分配给`package`的类型，那不是很好吗？如果我们的`package`是一个字符串，我们的`delivery`应该是一个字符串。如果是数字，我们的`delivery`应该是数字……以此类推。

这就是泛型的用武之地。让我们用泛型来写同样的函数:

```
function courier<T>(package: T): T {
  return package
}

// delivery is typed as string
const delivery = courier('book')

// lucknumber is typed as number
const luckynumber = courier(7) 
```

`T`是一个`type variable`。你不需要使用`T`，但这是标准做法。基本上，这个语法读作:“Courier 接受一个泛型类型，表示为`T`。这个泛型类型是由我们的`package`参数定义的，我们的 courier 函数返回一个相同类型的值。”

也许我们的`courier`应该可以一次接受多个包裹。

```
function courier<T>(package: T[]): T[] {
  return package
}

// typed as [string, number]
const delivery = courier(['book', 7]) 
```

现在你知道了。我们的 courier 函数现在接受任意长的包数组，并在返回值中正确输入该数组。

这仅仅是个开始，但是希望您能看到泛型对于创建可重用的、灵活的函数和类是多么的有用。

(这是一篇发表在我 benmcmahen.com 博客上的文章。点击这里[可以在线阅读。)](https://www.benmcmahen.com/generics-in-typescript/)