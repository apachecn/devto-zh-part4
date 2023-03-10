# 通过 JavaScript async await 解释 Haskell do 符号——第 1 部分

> 原文：<https://dev.to/gege251/haskell-do-notation-explained-through-javascript-async-await-part-1-3ha1>

这篇博客旨在为熟悉 JavaScript 的程序员介绍 Haskell 的 IO monad 和 *do* 符号。我假设您刚刚开始学习 Haskell，很难理解您的主函数中发生了什么。我将介绍 JavaScript 中的承诺具有单子性质的思想，如果您已经使用了承诺，它可以帮助您从总体上理解单子和 Haskell。

当我第一次学习 Haskell 时，我试着像对待任何其他新语言一样去做:从控制台请求一些输入，用给定的值做一些事情，然后在屏幕上输出一些东西。然而，在 Haskell 中这并不容易。

```
main :: IO ()
main = do
  putStrLn "Insert your name"
  yourName <- getLine
  let greeting = "Hello " ++ yourName ++ "!"
  putStrLn greeting 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，它看起来像任何其他命令式语言，但有两个奇怪的事情:

*   **做批注**——是什么？我为什么需要它？当我写一个函数时，它总是需要的吗？
*   **向左箭头**和**让**关键字——有什么区别？

为了回答第一个问题，do 符号是 Haskell 中一种特殊的语法，它允许您编写类似命令式的代码。然而，Haskell 的真正本质并不是命令式的，所以它只是一种隐藏了更多功能的语法糖。

所以让我们后退一点，想想是什么让某件事情变得必要或有用。有关键词，比如不变性，纯函数等等。但是我想强调的是，函数式语言是基于表达式的，而命令式语言是基于 T2 指令的。

```
// imperative style
let a = 5
if (b === true) {
    a = 10
}

// functional style
const a = b === true ? 10 : 5 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，第一部分是使用一个不可变的变量，当满足一个条件时，给出一个指令来改变这个变量。第二个例子在没有指令的情况下做了同样的事情。

当你用 JavaScript 写东西时，你会想到给计算机的指令，而在 Haskell 中，它更接近于某种数据管道。你不会找到类似上面的 if 语句(没有 else 块)，或者 for 循环，因为我们没有使用指令。一切都必须是一个表达式，或者一个有一些输入并返回一个输出的函数，**不做任何其他事情**。函数式语言有自己的一套工具来实现同样的事情，但有这些限制，比如映射器和缩减器(或折叠)而不是循环。当然还有单子而不是任意的副作用。

让我们回到第一个例子。你可能已经知道任何用 do 符号编写的函数也可以写成表达式:

```
main :: IO ()
main =
  putStrLn "Insert your name"
    >>= (\_ -> getLine)
    >>= (\yourName -> let greeting = "Hello " ++ yourName in putStrLn greeting) 
```

Enter fullscreen mode Exit fullscreen mode

更疯狂的事情发生了！`>>=`又出现了一些匿名函数。与此同时，左箭头消失了。很难理解这段代码，这是 do 符号存在的主要原因。

让我们试着把它分成几个小函数来看看所有的构建模块。记住，Haskell 就像一个乐高积木，你的功能就是一个个小积木。(我不建议把事情分解得这么小，我这么做只是希望能更好地了解这些积木是如何组合在一起的。)

```
main :: IO ()
main = giveInstruction >>= getName >>= outputGreeting

giveInstruction :: IO ()
giveInstruction = putStrLn "Insert your name"

getName :: () -> IO String
getName _ = getLine

outputGreeting :: String -> IO ()
outputGreeting yourName =
  let greeting = "Hello " ++ yourName in putStrLn greeting 
```

Enter fullscreen mode Exit fullscreen mode

`giveInstruction`将执行 IO，但只返回一个单元，这类似于其他语言中的 void。

我们想把`giveInstruction`的结果通过管道传递给`getName`，所以我们把一个单元作为参数。不过这不是必须的，使用`>>`操作符会更好，我只是用它来让我们的例子更像 JavaScript 版本。

`getName`的结果是一个字符串，所以它可以很容易地通过管道传递到最后一个函数中。

现在，这里有一个 Node.js 脚本做同样的事情:

```
process.stdin.setEncoding('utf-8')

const output = word => console.log(word)

const giveInstruction = () => output("Insert your name")

const getName = () => new Promise(resolve => process.stdin.once('data', resolve))

const outputGreeting = yourName => {
    const greeting = "Hello " + yourName
    output(greeting)
}

const createGreeting = yourName => "Hello `

const main = () => {
    giveInstruction()
    getName()
        .then(outputGreeting)
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

我们需要使用一个承诺来处理我们的用户输入。承诺包装了输入值，我们只能通过*然后*方法来访问它。现在想象一下，由于某种可疑的原因，我们想要延迟一秒钟的输出。现在输出函数返回一个承诺。

```
process.stdin.setEncoding('utf-8')

const output = word => new Promise(resolve => {
    setTimeout(() => {
        console.log(word)
        resolve()
    }, 1000)
})

const giveInstruction = () => output("Insert your name")

const getName = () => new Promise(resolve => process.stdin.once('data', resolve))

const outputGreeting = yourName => {
    const greeting = "Hello " + yourName
    return output(greeting)
}

const main = () => {
    giveInstruction()
        .then(getName)
        .then(outputGreeting)
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，您可能会看到与我们的 Haskell 代码有些相似之处。如果你想使用异步函数的结果，你必须使用 then 方法。`then`方法对于承诺的目的与`>>=`也称为`bind`对于 IO 单子的目的相同。我敢说 async await 语法和 do 符号的目的几乎一样:

```
const main = async () => {
    await giveInstruction()
    const yourName = await getName()
    await outputGreeting(yourName)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在去掉了 thens，但是必须将 getName 的结果保存到一个变量中，所以我们的代码失去了它的管道性质。同样重要的是要知道异步函数只是一个返回承诺的函数。这只是语法上的糖，就像 do 符号一样。

让我们更进一步，通过将逻辑从 IO 操作中分离出来，分解输出函数。新创建的`createGreeting`是一个纯函数，这意味着它不调用任何副作用，也不需要用任何单子包装。顺便说一下，将纯业务逻辑从副作用中分离出来被认为是一个很好的实践。这一次，我将再次使用 do 符号:

```
main :: IO ()
main = do
  giveInstruction
  yourName <- getName ()
  let greeting = createGreeting yourName
  outputGreeting greeting

giveInstruction :: IO ()
giveInstruction = putStrLn "Insert your name"

getName :: () -> IO String
getName _ = getLine

createGreeting :: String -> String
createGreeting yourName = "Hello " ++ yourName

outputGreeting :: String -> IO ()
outputGreeting greeting = putStrLn greeting 
```

Enter fullscreen mode Exit fullscreen mode

在 JS 中，我们会这样修改我们的程序:

```
const giveInstruction = () => output("Insert your name")

const getName = () => new Promise(resolve => process.stdin.once('data', resolve))

const createGreeting = yourName => "Hello " + yourName

const outputGreeting = yourName => output(greeting)

const main = async () => {
    await giveInstruction()
    const yourName = await getName()
    const greeting = createGreeting(yourName)
    await outputGreeting(yourName)
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

这应该回答了关于左和左箭头的问题。我们的 JS 实现在每一行都有 await 关键字，除了在 createGreeting 之前。这是因为它不是异步函数。

Haskell 代码也是如此:当我们希望从 IO 函数中获得一些值时，我们需要使用`<-`,但是 createGreeting 函数不是 monad，所以我们使用 let 绑定。

我希望这篇文章是有帮助的。下一次我计划用一些更复杂的例子做一些深入的探讨。

## 有些旁注

我并不打算触及这个领域，但是在我写作的时候，我认为这部分需要一些解释:为什么单子不需要像其他普通函数一样在它们的类型签名中有一个`->`。`giveInstructions :: IO ()`函数就是一个很好的例子。如果你看它的签名，它甚至不像一个函数。事实上，它不是。它是效果的返回值，包装在 IO 单子中。这意味着严格来说，我们的 JavaScript 应该是这样的:

```
const giveInstruction: Promise<void> = output("Insert your name") 
```

Enter fullscreen mode Exit fullscreen mode

当然，在 JavaScript 中，它会在程序启动时立即运行输出函数。为了延迟函数求值，我们把它包装在一个函数里，不需要参数。

你可能已经知道了，但是 Haskell 是一种延迟评估语言，这意味着一个函数或效果只有在需要的时候才会被评估。所以如果你的代码中有一个未使用的值，它不会被计算。这意味着只有在`main`函数中使用`giveInstruction`值时，才会对其进行评估。

继续阅读第二部分