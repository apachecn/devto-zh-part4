# 结果:合成和错误处理

> 原文：<https://dev.to/patroza/result-composition-and-error-handling-m8i>

我们可以通过利用一个结果类和来自函数式编程世界的几个其他工具来改进我们的错误处理和组合。

我们包装结果，而不是抛出错误。在记录可能错误的过程中，结果要么是错误值，要么是成功值。调用者必须首先检查并打开结果，处理成功或失败的情况。为更多的函数式编程和组合铺平了道路。

有关结果类和面向铁路的编程的更完整介绍:

*   [https://fsharpforfunandprofit.com/rop/](https://fsharpforfunandprofit.com/rop/)
*   [https://dev . to/_ gdelgado/type-safe-error-handling-in-typescript-1p4n](https://dev.to/_gdelgado/type-safe-error-handling-in-typescript-1p4n)
*   [https://khalilstemmler . com/articles/enterprise-typescript-nodejs/handling-errors-result-class/](https://khalilstemmler.com/articles/enterprise-typescript-nodejs/handling-errors-result-class/)

关于实现，请查看[GitHub #面向铁路的编程](https://github.com/topics/railway-oriented-programming)；多种编程语言中的 ROP(**Python/Go/Java/c#/F #**等)

在这些系列中，我将分享我在(激动人心的)旅程中的发现。

## 祈使样

```
const r = doSomeAction() // Result<string, SomeVariableIsInvalid | ServiceUnavailableError>
if (r.isErr()) { // r: Error<SomeVariableIsInvalid | ServiceUnavailableError>
  if (r.error instanceof SomeVariableIsInvalid) {
    ctx.body = r.error.message
    ctx.statusCode = 400
  } else {
    ctx.statusCode = 500
  }
  return
}
// r: Ok<string>
ctx.body = r.value
ctx.statusCode = 200 
```

`doSomeAction`可以这样实现:

```
function doSomeAction(): Result<string, SomeVariableIsInvalid | ServiceUnavailableError> {
  if (!someVariableIsValid) {
    return err(new SomeVariableIsInvalid("some variable is not valid")
  }
  if (!isServiceAvailable()) {
    return err(new ServiceUnavailableError("The service is currently unavailable")
  }

  return ok("success response")
} 
```

## 功能样本

```
doSomeAction() // Result<string, SomeVariableIsInvalid | ServiceUnavailableError>
  .map(value => {
    ctx.body = value
    ctx.statusCode = 200
  })
  .mapErr(error => {
    if (error instanceof SomeVariableIsInvalid) {
      ctx.body = error.message
      ctx.statusCode = 400
    } else {
      ctx.statusCode = 500
    }
  }) 
```

所有的“操作符”都必须依赖于结果对象，因此扩展更加困难。(这类似于例如 RxJS 是如何启动的)

## 功能成分

```
doSomeAction() // Result<string, SomeVariableIsInvalid | ServiceUnavailableError>
  .pipe(
    map(value => {
      ctx.body = value
      ctx.statusCode = 200
    }),
    mapErr(error => {
      if (error instanceof SomeVariableIsInvalid) {
        ctx.body = error.message
        ctx.statusCode = 400
      } else {
        ctx.statusCode = 500
      }
    })
  ) 
```

运营商现在只是功能，易于扩展和滚动我们自己的；-) ( [RxJS v5.5](https://blog.hackages.io/rxjs-5-5-piping-all-the-things-9d469d1b3f44) 用户在这里可能会看到一些相似之处)

## 数据最后

```
const pipeline = pipe(
  map(value => {
    ctx.body = value
    ctx.statusCode = 200
  }),
  mapErr(error => {
    if (error instanceof SomeVariableIsInvalid) {
      ctx.body = error.message
      ctx.statusCode = 400
    } else {
      ctx.statusCode = 500
    }
  })
)

pipeline(doSomeAction()) 
```

所以`pipeline`现在是可重用的。要是 [tc39 提案-管道-运营商](https://github.com/tc39/proposal-pipeline-operator)能快点着陆就好了，这样我们就能得到隐藏一些锅炉板和句法噪音的句法糖:)

## 建筑顶层

进一步分解成独立的功能，使它们变得可重用，或者分离抽象层次，使管道变得更容易阅读。

```
const writeSuccessResponse = value => {
  ctx.body = value
  ctx.statusCode = 200
}

const writeErrorResponse = error => {
  if (error instanceof SomeVariableIsInvalid) {
    ctx.body = error.message
    ctx.statusCode = 400
  } else {
    ctx.statusCode = 500
  }
}

const pipeline = pipe(
  map(writeSuccessResponse),
  mapErr(writeErrorResponse)
) 
```

进一步分解:

```
const writeSuccessResponse = value => {
  ctx.body = value
  ctx.statusCode = 200
}

const writeDefaultErrorResponse = error => {
  ctx.statusCode = 500
}

const writeSomeVariableIsInvalidErrorResponse = error => {
  if (error instanceof SomeVariableIsInvalid) {
    ctx.body = error.message
    ctx.statusCode = 400
  }
}

const pipeline = pipe(
  map(writeSuccessResponse),
  mapErr(writeDefaultErrorResponse),
  mapErr(writeSomeVariableIsInvalidErrorResponse),
) 
```

或许还有另一种选择:

```
const mapErrIf = (errorHandler: error => void, predicate: error => boolean) => 
  error => {
    if (!predicate(error)) { return }
    errorHandler(error)
  }
}
// usage
mapErrIf(_ => ctx.statusCode = 400, error => error instanceOf SomeVariableIsInvalid) 
```

当然还有许多其他选择和组成形式，让这是读者的练习；-)

## 框架和示例代码

在探索这些主题的同时，我正在开发一个应用程序框架，它广泛地利用了管道组合，包括示例应用程序！

源代码:

*   [fp-app 框架](https://github.com/patroza/fp-app-framework)
    *   [示例应用](https://github.com/patroza/fp-app-framework/tree/master/samples/basic)
    *   [不要投掷延伸管](https://github.com/patroza/fp-app-framework/tree/master/packages/neverthrow-extensions)
    *   使用 [neverthrow fork](https://github.com/patroza/neverthrow) (分叉自 [gDelgado14/neverthrow](https://github.com/gDelgado14/neverthrow)

## 接下来是什么

在本系列的下一篇文章中，我计划介绍更高级的概念，如`flatMap`、`toTup`、`tee`等:)

## 进一步阅读

请务必同时查看[gcanti/FP-ts](https://github.com/gcanti/fp-ts)；一个功能强大的面向编程的库，尤其是 v2 看起来很有前途，因为有相似的管道组成！