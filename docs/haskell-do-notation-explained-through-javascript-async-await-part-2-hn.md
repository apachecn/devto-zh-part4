# 通过 JavaScript async await 解释 Haskell do 符号——第 2 部分

> 原文：<https://dev.to/gege251/haskell-do-notation-explained-through-javascript-async-await-part-2-hn>

你好。这是我关于 Haskell 的小教程的第二部分。在第一部分中，我们看了一些使用简单 IO 效果的简单 Haskell 示例，以及用 JavaScript 编写的类似程序，其中每个 IO 效果都返回一个承诺，以发现 JS 承诺和 Haskell IO monad 之间的相似之处。

这次我将探索一些更复杂的想法:如何处理序列效应。

首先，让我们看一个非常简单的例子:我们将创建一个程序

*   从用户输入中读取数字 n
*   将 n 行用户输入读入一个数字数组
*   将所有数字加 100。

所以，对于输入来说

**2
3
5**

我们预计产量为

**103，105**

下面是它在命令式 JavaScript 中的样子:

```
process.stdin.setEncoding('utf-8')

const getNumber = () => new Promise(
    resolve => process.stdin.once('data', data => resolve(Number(data)))
)

const main = async () => {
    const n = await getNumber()
    const numbers = []
    for (let i = 0; i < n; i++) {
        const newNumber = await getNumber()
        numbers.push(newNumber + 100)
    }
    console.log(numbers)
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

然而，这在纯函数式语言中是行不通的，因为它使用可变变量。我们需要考虑数据以及数据如何流经我们的应用程序，而不是交给计算机处理的指令。我们还需要限制自己只使用不可变的值，以及 map、fold 等函数。

对于函数式编程的新手来说，这个解决方案可能有点违反直觉:我们会的

*   生成一个从 1 到 n 的数组
*   在这个数组上映射并评估我们有效的 getNumber 函数
*   将生成的数组打印到屏幕上

如果这一开始没有意义，就告诉我，希望下面的例子能让你明白。

首先，我们需要生成数组。函数式语言通常有一些强大的实用函数，用于生成数组等任务，但在 JS 中，我们必须自己实现它。

我们可以使用递归以一种很好的函数方式实现这一点，但这不是本文的重点，所以我写了一个更复杂的 JS 版本:

```
const range = (from, to) =>
    [...Array(to - from + 1)].map((_, index) => index + from) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以重新实现我们的主要功能。

```
const main = async () => {
    const n = await getNumber()
    const numbers = range(1, n).map(_ => getNumber())
    const mapped = numbers.map(x => x + 100)
    console.log(mapped)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的 range 函数生成一个从 1 到 n 的数组，然后我们将每个数字映射到`getNumber`函数，丢弃原始数组的数字。

甜食...如果可行的话。但是我们有一个问题:`getNumber`返回一个承诺，所以我们的`numbers`变量将是一个承诺数组，但是我们想要一个数字数组。我们不能摆脱承诺，但我们可以把它们聚合成一个。JavaScript 有一个名为 [Promise.all](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) 的内置函数可以做到这一点。让我们将数组传递给 Promise.all，并在它之前放置一个 await，以便从承诺中获取解析后的值。

```
const main = async () => {
const n = await getNumber()
    const numbers = await Promise.all(range(1, n).map(_ => getNumber()))
    const mapped = numbers.map(x => x + 100)
    console.log(mapped)
} 
```

Enter fullscreen mode Exit fullscreen mode

瞧。实际上，它仍然有一个 bug，与我们对`getNumber`的实现有关。我们的程序现在用相同的值解析第一个用户输入的所有承诺。一个不太实用的解决方案:

```
const queue = []

const getNumber = () => new Promise(resolve => {
    queue.push(input => resolve(Number(input)))
})

process.stdin.on('data', data => {
    const nextResolver = queue.shift()
    nextResolver(data)
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们深入 Haskell，用同样的方法:

```
main :: IO ()
main = do
  n       <- getNumber
  numbers <- sequence (map (\_ -> getNumber) [1 .. n])
  let mapped = map (100 +) numbers
  print mapped

getNumber :: IO Int
getNumber = fmap read getLine 
```

Enter fullscreen mode Exit fullscreen mode

Haskell 有一个更通用的函数叫做`sequence`，而不是 Promise 特定的 Promise.all。它的类型签名写着`(Traversable t, Monad m) => t (m a) -> m (t a)`。`t`和`m`是类型变量，其中`t`必须是可遍历的，`m`必须是单子。Traversable 和 Monad 是类型类，所以这个函数不是特定于列表的，而是对可遍历类型类中的每个类型都是多态的。

如果我们用程序中的具体类型替换类型变量，我们得到:`[IO Integer] -> IO [Integer]`。请记住，当我们在示例中添加 Promise.all 时，我们需要将承诺数组的**转换为数组**的**承诺。这次我们需要将 IO 单子的**列表**转换成列表**的 **IO 单子。**

如果你看看 JS 和 Haskell 的例子，它们看起来非常相似。那是因为承诺是单子，所以你已经知道如何处理它们。当你迷失在 Haskell 的单子丛林中时，这些知识真的很有帮助。

Haskell 的 IO monad 和 JS 的 Promise 有很多共同点。当你处理一个承诺时，你不能简单地使用它的值，你必须使用`then`方法或者`async await`语法。此外，一旦你在函数中打开一个承诺，它本身就会变成一个异步函数，它会污染你的函数，就像 Haskell 中的 IO monad 一样。

## 关于类型类和多态性

类型类是可以使用同一组多态函数的类型组。类型类中的每个类型都必须实现一些基本的功能——如果你熟悉 OOP 概念，这种思想非常接近于实现接口。在本教程的第一部分，我们看到了 bind 或`>>=`函数的运行。这是每个单子都必须实现的基本功能之一。`sequence`使用此函数将列表中的值连接在一起。

举个例子，关于多态是如何工作的，这就是当你使用`sequence`和单子
时会发生的事情

```
> sequence [Just 4, Just 5, Just 6]
Just [4,5,6]
> sequence [Just 4, Nothing, Just 6]
Nothing 
```

Enter fullscreen mode Exit fullscreen mode

sequence 函数从左到右，使用 Maybe 类型的`>>=`的实现来连接列表中的值。一旦列表中出现 Nothing，那么`>>=`将返回 Nothing。

```
instance Monad Maybe where
    (Just x) >>= k = k x
    Nothing  >>= _ = Nothing 
```

Enter fullscreen mode Exit fullscreen mode

在 Haskell 中，许多类型类的名字来自范畴理论。单子是其中之一，但还有像幺半群，函子，应用等类。不过了解理论是好的，有浅薄的知识就能写 Haskell 就足够了。随着你对这门语言越来越熟悉，自然你也会学到更多关于范畴理论的知识。首先，很好理解的是，每个类型类都有一些能力，一些它可以使用的多态函数:函子可以用`fmap`映射，单子可以用`>>=`绑定。还有，因为每个单子都是函子，所以每个单子也可以映射。

## 单子的特殊映射函数

让我们回到我们的例子。可以使用一些名为`mapM`和`mapM_`的实用函数来进一步简化。

`mapM`的类型签名是`(Traversable t, Monad m) => (a -> m b) -> t a -> m (t b)`。这一个做同样的事情作为**序列和地图一起**。它将一元函数映射到一个列表，并收集结果。下面是我们简化的主要功能:

```
main :: IO ()
main = do
  n       <- getNumber
  numbers <- mapM (\_ -> getNumber) [1 .. n]
  let mapped = map (100 +) numbers
  print mapped 
```

Enter fullscreen mode Exit fullscreen mode

现在我们知道了如何做一个单子序列，让我们看看另一个例子:我们想一个接一个地输出我们的数字列表。

在 JS 中，我们可以简单地在数组中使用 forEach。我们现在将使用我们在第一部分中介绍的无意义的异步`output`函数:

```
const output = word => new Promise(resolve => {
    setTimeout(() => {
        console.log(word)
        resolve()
    }, 1000)
})

const main = async () => {
const n = await getNumber()
    const numbers = range(1, n).map(_ => getNumber())
    const mapped = numbers.map(x => x + 100)
    mapped.forEach(output)
} 
```

Enter fullscreen mode Exit fullscreen mode

forEach 与 map 相同，但它忽略返回值。在某些情况下，忽略返回似乎是可以的，但是如果我们想知道异步函数何时完成执行呢？`output`函数实际上返回一个`Promise<undefined>`。我们需要收集返回函数，并且只解析我们的主函数，当它们都被解析时。它将我们引向相同的解决方案，作为输入。

```
const output = word => new Promise(resolve => {
    setTimeout(() => {
        console.log(word)
        resolve()
    }, 1000)
})

const main = async () => {
    const n = await getNumber()
    const numbers = range(1, n).map(_ => getNumber())
    const mapped = numbers.map(x => x + 100)
    return Promise.all(mapped.map(output))
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们尝试在 Haskell 中使用相同的方法:

```
main :: IO ()
main = do
  n       <- getNumber
  numbers <- mapM (\_ -> getNumber) [1 .. n]
  let mappedNumbers = map (100 +) numbers
  mapM print mappedNumbers 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个类型错误:

```
 Couldn't match type ‘[()]’ with ‘()’
    Expected type: IO ()
    Actual type: IO [()] 
```

Enter fullscreen mode Exit fullscreen mode

main 函数恰好返回一个`IO [()]`。让我们看看是怎么回事:最后一行是`mapM print mappedNumbers`，这里打印的是`a -> IO ()`。如果我们用我们的具体类型替换抽象类型`mapM`，我们得到:`(a -> IO ()) -> [a] -> IO [()]`。

我们可以自己忽略 mapM 的返回值:

```
main :: IO ()
main = do
  n       <- getNumber
  numbers <- mapM (\_ -> getNumber) [1 .. n]
  let mappedNumbers = map (100 +) numbers
  _ <- mapM print mappedNumbers
  return () 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个更简单的版本，用`mapM_`忽略返回值:

`(Foldable t, Monad m) => (a -> m b) -> t a -> m ()`

`(a -> IO ()) -> [a] -> IO ()`

```
main :: IO ()
main = do
  n       <- getNumber
  numbers <- mapM (\_ -> getNumber) [1 .. n]
  let mappedNumbers = map (100 +) numbers
  mapM_ print mappedNumbers 
```

Enter fullscreen mode Exit fullscreen mode

我希望这一部分不是太令人生畏。下次再见！