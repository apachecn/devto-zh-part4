# 黑客攻击 JS async/await 来链接单子

> 原文：<https://dev.to/munizart/hacking-js-async-await-to-chain-monads-16ko>

# 米字。

```
then :: Monad m => m a ~> (a -> m b) -> m b 
```

# `await`一分钟

原生 JS 承诺跟随[https://promisesaplus.com/](https://dev.toA+%20promise%20spec)。符合 A+的 promises 实现可以互操作，但它是通过希望任何实现了`then`方法的东西都像 promises 一样运行来实现的，这就是我们将要侵入的地方。

从规格来看，对我们来说重要的部分是:

> 1.2“then able”是定义 then 方法的对象或函数。

[...]

> 2.2 诺言的 then 方法接受两个参数:
> `promise.then(onFulfilled, onRejected)`

[...]

> 2.2.2 如果`onFulfilled`是一个函数:
> 2.2.2.1 它必须在 promise 完成后被调用，promise 的值作为它的第一个参数。【2.2.2.2】在诺言实现之前，决不能打电话。2.2.2.3 它不能被调用超过一次。

最高有效位:

> [...]如果`x`是一个`thenable`，在假设`x`的行为至少有点像承诺的情况下，尝试做出承诺采用`x`的状态。否则，它用值`x`履行承诺。

所有这些意味着:
1 -我们必须实现一个`then`方法，来欺骗`The Promise Resolution Procedure`调用它。这将是`bind`行动的别名。
2 -到`2.2.2.3`时，我们的`then`将被提供给一个`onFulfilled`函数，该函数只需要一次调用，也就是说，链接枚举是不可能的。

# 整蛊 JS

考虑以下单子:

```
const Const = (x) => ({
  then (onFulfilled) {
    return onFulfilled(x)
  }
})

const distopy = Const(1000)
  .then(x => Const(x + 900))
  .then(x => Const(x + 80))
  .then(x => Const(x + 4)) // Const(1984) 
```

`then`的签名为:`then :: Const a ~> (a -> Const b) -> Const b`

现在，我想要一个给定`Const number`的函数，返回一个两者之和的`Const`。我只需要写一些像:

```
function sumConsts (constA, constB) {
  return constA
    .then(a => constB
      .then(b => Const(a + b)
    )
  )
} 
```

我们需要求和的`Const`越多，看起来就越像回调——地狱，所以我会利用`Const`是`thenable`的优势，将`sumConsts`重构为:

```
const sumConsts = async (constA, constB) => Const(await constA + await constB) 
```

但是现在，由于`async function` s 总是将一个承诺返回到返回值中，并且`Const`是一个`thenable`，承诺解析过程将开始，并使返回的承诺“试图采用”它的状态，因此将永远不会得到一个`Const`返回，但是由于`Const`和承诺实现了相同的接口，所以`Const`语义被保留。

# `Maybe`再如

```
const Maybe = {
 Just: (v) => {
   const typeofV = typeof v
   if (typeofV === 'undefined' || typeofV === 'null') {
     return Maybe.Nothing
   }

   return {
     then (onFulfilled) {
       return onFulfilled(v)
     }
   }
 },

 Nothing: {
   // You can either never call `onFulfilled`, so a Nothing never resolves.
   // then() {},

   // Or call `onRejected`, so resolving a Nothing rejects the promise
   then(onFulfilled, onRejected) {
     onRejected(Maybe.Nothing)
     return Maybe.Nothing
   }
 }
}

function flipCoin (myGuess) {
  const coin = Math.random() < 0.5 ? 'heads' : 'tails'
  if (coin === myGuess) {
    return Maybe.Just (myGuess)
  } else {
    return Maybe.Nothing
  }
}

async function playIt (guess = 'heads', tries = 1) {
  try {
    await flipCoin (guess)
    return tries
  } catch (reason) {
    if (reason === Maybe.Nothing)
      return playIt(guess, tries + 1)
    else
      throw reason
  }
}

playIt()
  .then(console.log) // avg output: 2 
```