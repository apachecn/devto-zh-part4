# 何时返回结果。错误以及何时抛出异常？

> 原文：<https://dev.to/patroza/when-to-return-a-result-error-and-when-to-throw-an-exception-5gbd>

在前一部分中，我介绍了 Result 类作为控制流和错误处理的一种手段。现在我将关注何时使用 Result 类返回错误，以及何时抛出。

在这个问题上似乎有相当多的不同意见:

1.  总是抛出一个错误
2.  总是抛出错误，然后只在需要处理它们的时候包装它们
3.  **只返回可恢复的错误，抛出其余的**
4.  **返回可预期的错误，抛出(或传递)其余的。**
5.  返回每个错误，从不抛出。(包装任何库错误)

我大部分时间都在 4 号阵营，虽然我认为 3 号和 4 号实际上是同一个意思，大部分时间。一如既往，这取决于用例。

我通常会返回:**域**错误和**某些** **基础设施**错误

*   *售罄*、*房间已预订*、*卡纸*、*无效产品代码*、*代码已退款*、*支付方式失败*
*   *验证错误*，*无效状态错误*，*禁止生成错误*，*验证要求在*后面
*   *API error*/*db error*:*record not found*，*record 已经存在*，*(乐观)locker error*，*couldacquirelocerror*， *ConnectionError* ， *RemoteServiceError*

我会抛出异常(恐慌):

程序员错误:

*   *ArgumentException*
*   *NullException*
*   *DivideByZeroException*

损坏错误，例如:*序列化异常*

*   运行时也会抛出各种错误，比如在 *StackOverflow* 或者*内存不足*的情况下。这种错误应该放弃当前流程，并引发一个异常，可能在最高级别捕获，通常用于错误日志记录。

最终，这取决于你在建造什么。

## 抓&裹‘所有的廷格’？

这是否意味着您应该捕捉并包装所有第三方错误？不，它遵循同样的原则；如果它陷入可预期的错误，如表示 api 调用状态代码(如 400 或 404)的错误，或者例如没有找到数据库记录；包起来。

## 为什么返回而不是抛出

> 这种技术允许您优雅地捕获错误，而不会用难看的条件和 try/ catch 语句污染您的代码。

*出自《领域建模变得功能化》一书；很棒的一篇阅读，真心推荐！*

*   try/catch/finally 是冗长的，缩进/嵌套至少 1 个作用域深度，通常还会导致多层深度(在 1 个或多个函数内)
*   错误成为返回类型签名的一部分；自动文档，以及在缺少处理时的编译器帮助
*   它支持可组合性
*   抛出错误是很昂贵的(栈跟踪等)，而对于你将返回的错误，通常我认为栈跟踪没有用。现在我要第一个说:不要过早地优化，但是如果你能从头开始设计你的应用程序，即使在可预期的错误情况下，也是高性能的；太好了。

顺便说一下，在 javascript 中:

*   除了`if`语句或`switch`之外，没有“漂亮的”内置方式来不同地处理不同的错误类型，不像在 C#或 Python 中那样。
*   不像在 Java 中，没有“漂亮的”内置方法来记录可能发生的错误，并对它们进行类型安全保护。

## 其他支持返回状态而非异常的实现示例

*   **fetch**:fetch 实现只会抛出一个网络错误。它将使用状态码> 400 为假的`ok`布尔值。尽管它没有 Result 类优雅。
*   **承诺**:一旦解决或拒绝；不是成功就是错误。却有`await`支持的好处。

## 与“已检查”异常的关系

[https://stack overflow . com/questions/613954/the-case-against-checked-exceptions](https://stackoverflow.com/questions/613954/the-case-against-checked-exceptions)

[https://software engineering . stack exchange . com/questions/150837/maybe-monad-vs-exceptions](https://softwareengineering.stackexchange.com/questions/150837/maybe-monad-vs-exceptions)

或许可以总结为:

> 能力越大，责任越大

我认为区别在于，检查异常是一个强制的概念，但结果可以是您做出的应用程序级别的决定，您选择加入。

事实上如果你不想处理一个结果。错误，你可以让它冒泡，没有处理它的要求，除非你明确决定“这是我想以某种方式处理每个异常的地方”，例如使用 Typescript 的“穷举开关块”或类似的东西。

最后，在 Typescript 和 F#中，有联合类型可以帮助你将错误“折叠”到更高阶的错误中，不像 Java 中的检查异常。

## 违反开/关原则？

[https://stack overflow . com/questions/54882275/do-checked-exceptions-violate-the-open-closed-principle](https://stackoverflow.com/questions/54882275/do-checked-exceptions-violate-the-open-closed-principle)

OCP 似乎适用于模块，而不是方法。此外，我认为，如果你增加一个新的例外，你更“默默地”打破了合同。至少返回错误类型不会说谎；-)

## 错误处理样本

处理成功或错误情况

```
doSomething()
  .pipe(match(
    value => ctx.body = value,
    err => ctx.statusCode = 500,
  )) 
```

退回到默认值

```
const result = doSomething()
  .pipe(orDefault(“some-default-value”))
// if doSomething() fails, use “some-default-value” instead. 
```

高级 REST api 错误处理程序(欢迎更好的想法！)

```
if (err instanceof RecordNotFound) {
  ctx.body = { message }
  ctx.status = 404
} else if (err instanceof CombinedValidationError) {
  const { errors } = err
    ctx.body = {
    fields: combineErrors(errors),
    message,
  }
  ctx.status = 400
} else if (err instanceof FieldValidationError) {
  ctx.body = {
    fields: { [err.fieldName]: err.error instanceof CombinedValidationError ? combineErrors(err.error.errors) : err.message },
    message,
  }
  ctx.status = 400
} else if (err instanceof ValidationError) {
  ctx.body = { message }
  ctx.status = 400
} else if (err instanceof InvalidStateError) {
  ctx.body = { message }
  ctx.status = 422
} else if (err instanceof ForbiddenError) {
  ctx.body = { message }
  ctx.status = 403
} else if (err instanceof OptimisticLockError) {
  ctx.status = 409
} else if (err instanceof CouldNotAquireDbLockError) {
  ctx.status = 503
} else if (err instanceof ConnectionError) {
  ctx.status = 504
} else {
  // Unknown error
  ctx.status = 500
} 
```

这将伴随着一个针对状态 500 的无所不包的异常处理程序，并记录:

```
try {
  // execute the workflow
  // handle success and error result cases
} catch (err) {
  logger.error(err)
  ctx.status = 500
} 
```

## 来源

和往常一样，您也可以在 [patroza/fp-app-framework](https://github.com/patroza/fp-app-framework) 找到完整的框架和示例源代码

## 其他写着

*   [https://blog . log rocket . com/elegant-error-handling-with-the-JavaScript-any-monad-76 C7 AE 4924 a 1/](https://blog.logrocket.com/elegant-error-handling-with-the-javascript-either-monad-76c7ae4924a1/)

## 接下来是什么

在本系列的下一篇文章中，我计划探索我对未来的理想愿景，一旦我们在 Typescript 中获得管道操作符`|>`和改进的生成器类型支持；-)