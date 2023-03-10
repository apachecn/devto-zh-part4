# 建立一个零承诺

> 原文：<https://dev.to/_staticvoid/construindo-uma-promise-do-zero-4ndp>

一段时间前，我发表了一篇关于[我们如何一劳永逸地理解承诺](https://dev.to/khaosdoctor/entendendo-promises-de-uma-vez-por-todas-44i7)的文章，如果你还没读过的话，我推荐阅读，这样我们就可以继续，但我会对我们在其中谈的内容做一个简短的总结。

Promises 是处理以后在运行时可能获得的值的结构，例如，对外部服务器的请求或读取文件。鲜为人知的是，事实上 Promise 是一种可以通过使用对象导向和在[【promise+](https://promisesaplus.com/)中描述的规格来实现的设计模式。

既然最好的学习方式是做，那么我们就沉浸在诺言的规范中，从零开始实现自己的诺言吧！

## 规格

JavaScript 中已知的 Promises 规范位于 promises/a+(promes/a[的演变)中，因为在本机实现之前，一些库(如 q 和 Bluebird)已经实现了此模式。于是我们决定创建一个开放的规范，让实施该模型的人可以与其他程序员一起编写和讨论这些问题。本规范基本上定义了`then`方法的工作方式，因此符合该方法的所有承诺在任何地方都必须以相同的方式工作。](http://wiki.commonjs.org/wiki/Promises/A)

### 术语

让我们命名一些东西，首先定义我们承诺中要使用的所有术语，这是规范的直接翻译:

*   `Promise`是具有`then`方法的物体，其行为符合本规范
*   `then`是定义`then`方法的对象或函数
*   un**值**是 JavaScript 中的任何有效值(包括`undefined`、`thenable`或直到另一个`promise`
*   `exception`是从`throw`中提出的标准发展异常
*   A **理由**是一个`promise`被拒绝的原因(当他遭受`exception`时)

### Estado

Promise 基本上是一台状态机。它可能处于以下三种状态之一:

*   **待定:**在这种状态下她可以去`fulfilled`或`rejected`
*   **【填写完毕】**在这种状态下，承诺不能过渡到任何其他状态；还必须具有一个*值*即**不得**必须是可变更的
*   **拒绝(拒绝):**在这种状态下，承诺不能过渡到任何其他状态；还必须有一个*理由*即**不得**必须是可变更的

### 然后

所有承诺都需要指定一种`then`方法，该方法实际上将负责评估函数并返回当前值。方法`then`必须具有以下签名:

```
promise.then(onFulfilled, onRejected) 
```

Enter fullscreen mode Exit fullscreen mode

其中，`onFulfilled`是具有以下签名的函数:

```
(value: T) => void 
```

Enter fullscreen mode Exit fullscreen mode

E `onRejected`具有相同的签名，但具有**的原因**而不是值。

此外，`then`需要遵循一系列规则才能按照规范进行考虑。我不会把它们全部放在这里，但我会把最重要的放在这里:

*   `onRejected`和`onRejected`都是`then`的可选参数，如果不是函数则应忽略
*   `onFulfilled`当我接受时，应在承诺解决后一律叫**，以承诺值为第一个参数。另外，他只能打一次电话。**
***   `onRejected`当我接受时，应在承诺被拒绝后一律叫**，以承诺理由为第一个论据。另外，他只能打一次电话。*****   `then`可以在同一个承诺中多次链接。承诺完成或被拒绝时，所有*处理程序*t1】必须按顺序执行。*   `then`必须返回另一个承诺****

 ****## 实施

为了开始实现我们的诺言，我们首先创建一个名为“`PromiseTypes.ts`”的文件，我们将使用 Typescript 使其具有一些类型，这样会使理解更加简单。在此文件中，我们将放置我们已经知道存在的全局类型，例如角色`onFulfilled`和`onRejected`、执行者等等。

我们先用承诺的可能状态创建一个枚举器:

```
export enum PromiseStates {
  PENDING,
  FULFILLED,
  REJECTED
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将创建我们将使用的支撑类型和 promise 基础类型:

```
export type ResolveFunction = (value: any) => any
export type RejectFunction = (reason: any) => any
export type Thennable = { then: (value: any) => TypePromise }
export type ExecutorFunction = (resolve: ResolveFunction, reject: RejectFunction) => void 
```

Enter fullscreen mode Exit fullscreen mode

我们称之为`executor`promise 将在其构造函数中得到的角色，它必须包含`resolve`和`reject`。同样的，我们为“`thennable`”创建了一个类型。我们还将创建另一种辅助类型，只是为了让我们的代码更漂亮，称为`nullable<T>`，它将有助于我们实现可能为 nulos 的元素:

```
export type Nullable<T> = T | null 
```

Enter fullscreen mode Exit fullscreen mode

### Máquina de estados

让我们从创建一个名为`TypePromise`的文件开始，让我们把我们的类命名为`TypePromise`以避免与原生实施 Promises 发生冲突，因为它只是一个国家机器，考虑到我们必须拥有的所有国家:

```
import { PromiseStates, ResolveFunction, RejectFunction, ExecutorFunction, Nullable, Thennable } from './PromiseTypes'

export class TypePromise {
  private state: PromiseStates = PromiseStates.PENDING
  private value: any = null
  private thenHandlers: HandlerFunction[] = []
} 
```

Enter fullscreen mode Exit fullscreen mode

看，我们有了一种新的类型，即‘t0’是我们承诺中有各种‘t1’或‘T2’时要传递的对象。在这些情况下，我们必须一次运行一个处理程序。每一个都是具有执行者两个角色的对象，我们将在文件中添加“`PromiseTypes.ts`”并导入到主文件中，我们的文件“`PromiseTypes.ts`”如下所示:

```
import { TypePromise } from './TypePromise'

export enum PromiseStates {
  PENDING,
  FULFILLED,
  REJECTED
}

export type ResolveFunction = (value: any) => any
export type RejectFunction = (reason: any) => any
export type ExecutorFunction = (resolve: ResolveFunction, reject: RejectFunction) => void
export type Thennable = { then: (value: any) => TypePromise }

export type Nullable<T> = T | null

export type HandlerFunction = {
  onFulfilled?: ResolveFunction;
  onRejected?: Nullable<RejectFunction>
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们把我们的诺言过渡到两个众所周知的价值——`FULFILLED`和`REJECTED` :

```
import { PromiseStates, ResolveFunction, RejectFunction, ExecutorFunction, Nullable, Thennable, HandlerFunction } from './PromiseTypes'

export class TypePromise {
  private state: PromiseStates = PromiseStates.PENDING
  private value: any = null
  private thenHandlers: HandlerFunction[] = []

  private fulfill (value: any) {
    this.state = PromiseStates.FULFILLED
    this.value = value
  }

  private reject (reason: any) {
    this.state = PromiseStates.REJECTED
    this.value = reason
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，转换只是状态更改器。是他们要结束一个承诺并箭头其最终价值。

现在，我们将创建另一个名为“`resolve`”的过渡，此过渡将负责执行承诺本身，并确定承诺是否已被解决或拒绝，以及我们是否收到另一个承诺。

```
import { PromiseStates, ResolveFunction, RejectFunction, ExecutorFunction, Nullable, Thennable, HandlerFunction } from './PromiseTypes'

export class TypePromise {
  private state: PromiseStates = PromiseStates.PENDING
  private value: any = null
  private thenHandlers: HandlerFunction[] = []

  private fulfill (value: any) {
    this.state = PromiseStates.FULFILLED
    this.value = value
  }

  private reject (reason: any) {
    this.state = PromiseStates.REJECTED
    this.value = reason
  }

  private resolve (result: any) {
    try {
        const then = this.getThen(result)
        if (then) return this.doResolve(then.bind(result), this.resolve, this.reject)
        this.fulfill(result)
    } catch (error) {
        this.reject(error)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的角色`resolve`基本上负责确定我们收到的角色是否为`thennable`对象，如果是，那么它将调用一个名为`doResolve`的新角色，该角色将带领执行者并代理执行我们自己 promise 的内部方法，一旦 promise 就将调用这些方法首先实施负责提取或忽略功能`thennable` :
的`getThen`

```
import { PromiseStates, ResolveFunction, RejectFunction, ExecutorFunction, Nullable, Thennable, HandlerFunction } from './PromiseTypes'

export class TypePromise {
  private state: PromiseStates = PromiseStates.PENDING
  private value: any = null
  private thenHandlers: HandlerFunction[] = []

  private fulfill (value: any) {
    this.state = PromiseStates.FULFILLED
    this.value = value
  }

  private reject (reason: any) {
    this.state = PromiseStates.REJECTED
    this.value = reason
  }

  private resolve (result: any) {
    try {
        const then = this.getThen(result)
        if (then) return this.doResolve(then.bind(result), this.resolve, this.reject)
        this.fulfill(result)
    } catch (error) {
        this.reject(error)
    }
  }

  private getThen (value: Thennable) {
    const valueType = typeof value
    if (value && (valueType === 'object' || valueType === 'function')) {
      const then = value.then
      if (typeof then === 'function') return then
    }
    return null
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的函数，它接收一个对象`thennable`，并检查传递的值的类型是否为对象或函数(如果它--并且具有属性`then`，因此我们检查该属性是否为函数，以便仅返回此处理程序。

让我们转到`doResolve`方法，他是 promise 的主要方法，因为他会启动整个链。负责确保仅执行，并围绕用户传递的函数创建包装，以便在内部控制这些函数。

```
import { 
  PromiseStates, 
  ResolveFunction, 
  RejectFunction, 
  ExecutorFunction, 
  Nullable, 
  Thennable, 
  HandlerFunction 
} from './PromiseTypes'

enum ReturnType {
  SUCCESS = 'success',
  ERROR = 'error'
}

export class TypePromise {
  private state: PromiseStates = PromiseStates.PENDING
  private value: any = null
  private thenHandlers: HandlerFunction[] = []

  private fulfill (value: any) {
    this.state = PromiseStates.FULFILLED
    this.value = value
  }

  private reject (reason: any) {
    this.state = PromiseStates.REJECTED
    this.value = reason
  }

  private resolve (result: any) {
    try {
        const then = this.getThen(result)
        if (then) return this.doResolve(then.bind(result), this.resolve, this.reject)
        this.fulfill(result)
    } catch (error) {
        this.reject(error)
    }
  }

  private getThen (value: Thennable) {
    const valueType = typeof value
    if (value && (valueType === 'object' || valueType === 'function')) {
      const then = value.then
      if (typeof then === 'function') return then
    }
    return null
  }

  private getHandlerType (type: ReturnType, done: boolean, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    return (value: any) => {
      if (done) return
      done = true
      return { error: onRejected, success: onFulfilled }[type](value)
    }
  }

  private doResolve (resolverFn: ExecutorFunction, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    let done = false
    try {
            resolverFn(this.getHandlerType(ReturnType.SUCCESS, done, onFulfilled, onRejected), this.getHandlerType(ReturnType.ERROR, done, onFulfilled, onRejected))
    } catch (error) {
      if (done) return
      done = true
      onRejected(error)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们做了一些额外的事情。首先，我们的功能`doResolve`基本上检查我们的执行者是否可靠，它通过围绕我们的内部功能创建封套来做到这一点，以便它们只运行一次，这是在我们的功能`getHandlerType`中完成的，它基本上返回一个新的签名功能`(value: any)`如果是，我们只返回；如果不是，我们将根据我们想要的类型获取相应的函数--它可以是解析函数或拒绝函数，为此，我们将在文件顶部创建内部枚举`ReturnType`，然后通过返回其值来执行该函数。

最后，我们将对我们的承诺决议做一点修改，这样我们就可以创建一系列要兑现的承诺。为此，我们将创建一个名为`HandlerFunction`的方法，负责接收和执行一个对象`HandlerFunction`，并创建一个辅助功能`executeAllHandlers`，该功能将遍历我们的处理程序数组并运行所有处理程序，直到不再有处理程序:

```
import { 
  PromiseStates, 
  ResolveFunction, 
  RejectFunction, 
  ExecutorFunction, 
  Nullable, 
  Thennable, 
  HandlerFunction 
} from './PromiseTypes'

enum ReturnType {
  SUCCESS = 'success',
  ERROR = 'error'
}

export class TypePromise {
  private state: PromiseStates = PromiseStates.PENDING
  private value: any = null
  private thenHandlers: HandlerFunction[] = []

  private fulfill (value: any) {
    this.state = PromiseStates.FULFILLED
    this.value = value
        this.executeAllHandlers()
  }

  private reject (reason: any) {
    this.state = PromiseStates.REJECTED
    this.value = reason
    this.executeAllHandlers()
  }

  private executeAllHandlers () {
    this.thenHandlers.forEach(this.executeHandler)
    this.thenHandlers = []  
  }

  private executeHandler (handler: HandlerFunction) {
    if (this.state === PromiseStates.PENDING) return this.thenHandlers.push(handler)
    if (this.state === PromiseStates.FULFILLED && typeof handler.onFulfilled === 'function') return handler.onFulfilled(this.value)
    if (this.state === PromiseStates.REJECTED && typeof handler.onRejected === 'function') return handler.onRejected(this.value)
  }

  private resolve (result: any) {
    try {
        const then = this.getThen(result)
        if (then) return this.doResolve(then.bind(result), this.resolve, this.reject)
        this.fulfill(result)
    } catch (error) {
        this.reject(error)
    }
  }

  private getThen (value: Thennable) {
    const valueType = typeof value
    if (value && (valueType === 'object' || valueType === 'function')) {
      const then = value.then
      if (typeof then === 'function') return then
    }
    return null
  }

  private getHandlerType (type: ReturnType, done: boolean, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    return (value: any) => {
      if (done) return
      done = true
      return { error: onRejected, success: onFulfilled }[type](value)
    }
  }

  private doResolve (resolverFn: ExecutorFunction, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    let done = false
    try {
            resolverFn(this.getHandlerType(ReturnType.SUCCESS, done, onFulfilled, onRejected), this.getHandlerType(ReturnType.ERROR, done, onFulfilled, onRejected))
    } catch (error) {
      if (done) return
      done = true
      onRejected(error)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们的状态机。

### Expondo métodos

既然我们刚刚构建了自己的状态机及其过渡，现在我们将创建用户可以执行的方法，如`then`、`catch`等。首先，我们将添加一种方法来解析承诺，创建其构造函数:

```
import { 
  PromiseStates, 
  ResolveFunction, 
  RejectFunction, 
  ExecutorFunction, 
  Nullable, 
  Thennable, 
  HandlerFunction 
} from './PromiseTypes'

enum ReturnType {
  SUCCESS = 'success',
  ERROR = 'error'
}

export class TypePromise {
  private state: PromiseStates = PromiseStates.PENDING
  private value: any = null
  private thenHandlers: HandlerFunction[] = []

  constructor (executor: ExecutorFunction) {
    this.resolve = this.resolve.bind(this)
    this.reject = this.reject.bind(this)
    this.executeHandler = this.executeHandler.bind(this)
    this.doResolve(executor, this.resolve, this.reject)
  }

  private fulfill (value: any) {
    this.state = PromiseStates.FULFILLED
    this.value = value
        this.executeAllHandlers()
  }

  private reject (reason: any) {
    this.state = PromiseStates.REJECTED
    this.value = reason
    this.executeAllHandlers()
  }

  private executeAllHandlers () {
    this.thenHandlers.forEach(this.executeHandler)
    this.thenHandlers = []  
  }

  private executeHandler (handler: HandlerFunction) {
    if (this.state === PromiseStates.PENDING) return this.thenHandlers.push(handler)
    if (this.state === PromiseStates.FULFILLED && typeof handler.onFulfilled === 'function') return handler.onFulfilled(this.value)
    if (this.state === PromiseStates.REJECTED && typeof handler.onRejected === 'function') return handler.onRejected(this.value)
  }

  private resolve (result: any) {
    try {
        const then = this.getThen(result)
        if (then) return this.doResolve(then.bind(result), this.resolve, this.reject)
        this.fulfill(result)
    } catch (error) {
        this.reject(error)
    }
  }

  private getThen (value: Thennable) {
    const valueType = typeof value
    if (value && (valueType === 'object' || valueType === 'function')) {
      const then = value.then
      if (typeof then === 'function') return then
    }
    return null
  }

  private getHandlerType (type: ReturnType, done: boolean, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    return (value: any) => {
      if (done) return
      done = true
      return { error: onRejected, success: onFulfilled }[type](value)
    }
  }

  private doResolve (resolverFn: ExecutorFunction, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    let done = false
    try {
            resolverFn(this.getHandlerType(ReturnType.SUCCESS, done, onFulfilled, onRejected), this.getHandlerType(ReturnType.ERROR, done, onFulfilled, onRejected))
    } catch (error) {
      if (done) return
      done = true
      onRejected(error)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们的构造函数仅箭头初始值并调用函数`doResolve`，从而开始解析 promise。

### 观察

为了遵守诺言，我们使用`then`表示成功，或`catch`表示错误。让我们创造我们的第一个公共方法`then` :

```
import { 
  PromiseStates, 
  ResolveFunction, 
  RejectFunction, 
  ExecutorFunction, 
  Nullable, 
  Thennable, 
  HandlerFunction 
} from './PromiseTypes'

enum ReturnType {
  SUCCESS = 'success',
  ERROR = 'error'
}

export class TypePromise {
  private state: PromiseStates = PromiseStates.PENDING
  private value: any = null
  private thenHandlers: HandlerFunction[] = []

  constructor (executor: ExecutorFunction) {
    this.resolve = this.resolve.bind(this)
    this.reject = this.reject.bind(this)
    this.executeHandler = this.executeHandler.bind(this)
    this.doResolve(executor, this.resolve, this.reject)
  }

  private fulfill (value: any) {
    this.state = PromiseStates.FULFILLED
    this.value = value
        this.executeAllHandlers()
  }

  private reject (reason: any) {
    this.state = PromiseStates.REJECTED
    this.value = reason
    this.executeAllHandlers()
  }

  private executeAllHandlers () {
    this.thenHandlers.forEach(this.executeHandler)
    this.thenHandlers = []  
  }

  private executeHandler (handler: HandlerFunction) {
    if (this.state === PromiseStates.PENDING) return this.thenHandlers.push(handler)
    if (this.state === PromiseStates.FULFILLED && typeof handler.onFulfilled === 'function') return handler.onFulfilled(this.value)
    if (this.state === PromiseStates.REJECTED && typeof handler.onRejected === 'function') return handler.onRejected(this.value)
  }

  private resolve (result: any) {
    try {
        const then = this.getThen(result)
        if (then) return this.doResolve(then.bind(result), this.resolve, this.reject)
        this.fulfill(result)
    } catch (error) {
        this.reject(error)
    }
  }

  private getThen (value: Thennable) {
    const valueType = typeof value
    if (value && (valueType === 'object' || valueType === 'function')) {
      const then = value.then
      if (typeof then === 'function') return then
    }
    return null
  }

  private getHandlerType (type: ReturnType, done: boolean, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    return (value: any) => {
      if (done) return
      done = true
      return { error: onRejected, success: onFulfilled }[type](value)
    }
  }

  private doResolve (resolverFn: ExecutorFunction, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    let done = false
    try {
            resolverFn(this.getHandlerType(ReturnType.SUCCESS, done, onFulfilled, onRejected), this.getHandlerType(ReturnType.ERROR, done, onFulfilled, onRejected))
    } catch (error) {
      if (done) return
      done = true
      onRejected(error)
    }
  }

  then (onFulfilled?: ResolveFunction, onRejected?: Nulable<RejectFunction>): TypePromise {
    return new TypePromise((resolve: ResolveFunction, reject: RejectFunction) => {
      const handleResult = (type: ReturnType) => {
        return (result: any) => {
          try {
            const executorFunction = type === ReturnType.ERROR ? reject : resolve
            const checkFunction = type === ReturnType.ERROR ? onRejected : onFulfilled
            return (typeof checkFunction === 'function') ? executorFunction(checkFunction(result)) : executorFunction(result)
          } catch (error) {
            reject(error)
          }
        }
      }

      return this.done(handleResult(ReturnType.SUCCESS), handleResult(ReturnType.ERROR))
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的方法`then`实现了上述内容，它接收两个可选参数，并返回一个新承诺的实例。我们再次使用 hashmap 技术来选择要执行的函数。如果用户传递的函数实际上是函数，我们先执行这些函数，然后将结果传递给最终执行者，后者将解析 promise 否则，我们将只执行解析函数，该函数可以是`reject`或`resolve`。看我们有了一个新方法，即‘T3’。

`done(onFulfilled, onRejected)`功能的语义比`then`更容易理解，虽然一个使用另一个，以便能够完成承诺。`done`功能遵循以下规则:

*   两个参数中只有一个被调用
*   只能打一次电话

此功能也只能在事件循环的“`tick`”结束时运行，因此我们必须确保始终将其调度为此运行。为此，我们将使用 API `process.nextTick`，该 API 将安排函数在节点的微任务队列中运行(请参阅[【本指南】](https://github.com/khaosdoctor/my-notes/blob/master/node/node-under-the-hood.md)，并始终在事件循环结束时运行:

```
import { 
  PromiseStates, 
  ResolveFunction, 
  RejectFunction, 
  ExecutorFunction, 
  Nullable, 
  Thennable, 
  HandlerFunction 
} from './PromiseTypes'

enum ReturnType {
  SUCCESS = 'success',
  ERROR = 'error'
}

export class TypePromise {
  private state: PromiseStates = PromiseStates.PENDING
  private value: any = null
  private thenHandlers: HandlerFunction[] = []

  constructor (executor: ExecutorFunction) {
    this.resolve = this.resolve.bind(this)
    this.reject = this.reject.bind(this)
    this.executeHandler = this.executeHandler.bind(this)
    this.doResolve(executor, this.resolve, this.reject)
  }

  private fulfill (value: any) {
    this.state = PromiseStates.FULFILLED
    this.value = value
        this.executeAllHandlers()
  }

  private reject (reason: any) {
    this.state = PromiseStates.REJECTED
    this.value = reason
    this.executeAllHandlers()
  }

  private executeAllHandlers () {
    this.thenHandlers.forEach(this.executeHandler)
    this.thenHandlers = []  
  }

  private executeHandler (handler: HandlerFunction) {
    if (this.state === PromiseStates.PENDING) return this.thenHandlers.push(handler)
    if (this.state === PromiseStates.FULFILLED && typeof handler.onFulfilled === 'function') return handler.onFulfilled(this.value)
    if (this.state === PromiseStates.REJECTED && typeof handler.onRejected === 'function') return handler.onRejected(this.value)
  }

  private resolve (result: any) {
    try {
        const then = this.getThen(result)
        if (then) return this.doResolve(then.bind(result), this.resolve, this.reject)
        this.fulfill(result)
    } catch (error) {
        this.reject(error)
    }
  }

  private getThen (value: Thennable) {
    const valueType = typeof value
    if (value && (valueType === 'object' || valueType === 'function')) {
      const then = value.then
      if (typeof then === 'function') return then
    }
    return null
  }

  private getHandlerType (type: ReturnType, done: boolean, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    return (value: any) => {
      if (done) return
      done = true
      return { error: onRejected, success: onFulfilled }[type](value)
    }
  }

  private doResolve (resolverFn: ExecutorFunction, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    let done = false
    try {
            resolverFn(this.getHandlerType(ReturnType.SUCCESS, done, onFulfilled, onRejected), this.getHandlerType(ReturnType.ERROR, done, onFulfilled, onRejected))
    } catch (error) {
      if (done) return
      done = true
      onRejected(error)
    }
  }

  then (onFulfilled?: ResolveFunction, onRejected?: Nulable<RejectFunction>): TypePromise {
    return new TypePromise((resolve: ResolveFunction, reject: RejectFunction) => {
      const handleResult = (type: ReturnType) => {
        return (result: any) => {
          try {
            const executorFunction = type === ReturnType.ERROR ? reject : resolve
            const checkFunction = type === ReturnType.ERROR ? onRejected : onFulfilled
            return (typeof checkFunction === 'function') ? executorFunction(checkFunction(result)) : executorFunction(result)
          } catch (error) {
            reject(error)
          }
        }
      }

      return this.done(handleResult(ReturnType.SUCCESS), handleResult(ReturnType.ERROR))
    })
  }

  private done (onFulfilled?: ResolveFunction, onRejected?: Nullable<RejectFunction>) {
    process.nextTick(() => {
      this.executeHandler({
        onFulfilled,
        onRejected
      })
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法无非是在 tick 结尾运行我们的处理程序`then`。

### 接住

最后，我们将实施`catch`方法，以便用户能够捕获 promise 的错误。它相当简单，还利用了`done`功能，与`then`不同，`catch` **总会有一个类型为拒绝功能的论据:** 

```
import { 
  PromiseStates, 
  ResolveFunction, 
  RejectFunction, 
  ExecutorFunction, 
  Nullable, 
  Thennable, 
  HandlerFunction 
} from './PromiseTypes'

enum ReturnType {
  SUCCESS = 'success',
  ERROR = 'error'
}

export class TypePromise {
  private state: PromiseStates = PromiseStates.PENDING
  private value: any = null
  private thenHandlers: HandlerFunction[] = []

  constructor (executor: ExecutorFunction) {
    this.resolve = this.resolve.bind(this)
    this.reject = this.reject.bind(this)
    this.executeHandler = this.executeHandler.bind(this)
    this.doResolve(executor, this.resolve, this.reject)
  }

  private fulfill (value: any) {
    this.state = PromiseStates.FULFILLED
    this.value = value
        this.executeAllHandlers()
  }

  private reject (reason: any) {
    this.state = PromiseStates.REJECTED
    this.value = reason
    this.executeAllHandlers()
  }

  private executeAllHandlers () {
    this.thenHandlers.forEach(this.executeHandler)
    this.thenHandlers = []  
  }

  private executeHandler (handler: HandlerFunction) {
    if (this.state === PromiseStates.PENDING) return this.thenHandlers.push(handler)
    if (this.state === PromiseStates.FULFILLED && typeof handler.onFulfilled === 'function') return handler.onFulfilled(this.value)
    if (this.state === PromiseStates.REJECTED && typeof handler.onRejected === 'function') return handler.onRejected(this.value)
  }

  private resolve (result: any) {
    try {
        const then = this.getThen(result)
        if (then) return this.doResolve(then.bind(result), this.resolve, this.reject)
        this.fulfill(result)
    } catch (error) {
        this.reject(error)
    }
  }

  private getThen (value: Thennable) {
    const valueType = typeof value
    if (value && (valueType === 'object' || valueType === 'function')) {
      const then = value.then
      if (typeof then === 'function') return then
    }
    return null
  }

  private getHandlerType (type: ReturnType, done: boolean, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    return (value: any) => {
      if (done) return
      done = true
      return { error: onRejected, success: onFulfilled }[type](value)
    }
  }

  private doResolve (resolverFn: ExecutorFunction, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    let done = false
    try {
            resolverFn(this.getHandlerType(ReturnType.SUCCESS, done, onFulfilled, onRejected), this.getHandlerType(ReturnType.ERROR, done, onFulfilled, onRejected))
    } catch (error) {
      if (done) return
      done = true
      onRejected(error)
    }
  }

  then (onFulfilled?: ResolveFunction, onRejected?: Nulable<RejectFunction>): TypePromise {
    return new TypePromise((resolve: ResolveFunction, reject: RejectFunction) => {
      const handleResult = (type: ReturnType) => {
        return (result: any) => {
          try {
            const executorFunction = type === ReturnType.ERROR ? reject : resolve
            const checkFunction = type === ReturnType.ERROR ? onRejected : onFulfilled
            return (typeof checkFunction === 'function') ? executorFunction(checkFunction(result)) : executorFunction(result)
          } catch (error) {
            reject(error)
          }
        }
      }

      return this.done(handleResult(ReturnType.SUCCESS), handleResult(ReturnType.ERROR))
    })
  }

  private done (onFulfilled?: ResolveFunction, onRejected?: Nullable<RejectFunction>) {
    process.nextTick(() => {
      this.executeHandler({
        onFulfilled,
        onRejected
      })
    })
  }

  catch (onRejected: RejectFunction) {
    return new TypePromise((resolve: ResolveFunction, reject: RejectFunction) => {
      return this.done(resolve, (error: any) => {
        if(typeof onRejected === 'function') {
          try {
            return resolve(onRejected(error))
          } catch (error) {
            reject(error)
          }
        }
        return reject(error)
      })
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还返回了一个新的承诺，并验证过去的功能是否不是我们需要先解决的另一个承诺。

## 奖金:最后

`promise.prototype.finally`规范已生效一段时间，并已在 ES6 中运行。`finally`功能有一个简单的目的:就像我们在 try/catch 块中拥有的`finally`一样，一个承诺的`finally`始终是**在解析结束时执行，而不管该承诺是被解析还是被拒绝，但与观察者不同的是，比如】**

 **实现相对简单，我们将在我们的 promise 中创建一个名为`finalFunction`的属性，该属性将以空函数开始。目标是我们有一种方法`finally`，它会将此属性的值换成用户传递的函数，然后在方法`fulfill`或`reject` :
的末尾运行

```
import { 
  PromiseStates, 
  ResolveFunction, 
  RejectFunction, 
  ExecutorFunction, 
  Nullable, 
  Thennable, 
  HandlerFunction 
} from './PromiseTypes'

enum ReturnType {
  SUCCESS = 'success',
  ERROR = 'error'
}

export class TypePromise {
  private state: PromiseStates = PromiseStates.PENDING
  private finalFunction: Function = () => { }
  private value: any = null
  private thenHandlers: HandlerFunction[] = []

  constructor (executor: ExecutorFunction) {
    this.resolve = this.resolve.bind(this)
    this.reject = this.reject.bind(this)
    this.executeHandler = this.executeHandler.bind(this)
    this.doResolve(executor, this.resolve, this.reject)
  }

  private fulfill (value: any) {
    this.state = PromiseStates.FULFILLED
    this.value = value
        this.executeAllHandlers()
    this.finalFunction() // Executamos o finally
  }

  private reject (reason: any) {
    this.state = PromiseStates.REJECTED
    this.value = reason
    this.executeAllHandlers()
    this.finalFunction() // Executamos o finally
  }

  private executeAllHandlers () {
    this.thenHandlers.forEach(this.executeHandler)
    this.thenHandlers = []  
  }

  private executeHandler (handler: HandlerFunction) {
    if (this.state === PromiseStates.PENDING) return this.thenHandlers.push(handler)
    if (this.state === PromiseStates.FULFILLED && typeof handler.onFulfilled === 'function') return handler.onFulfilled(this.value)
    if (this.state === PromiseStates.REJECTED && typeof handler.onRejected === 'function') return handler.onRejected(this.value)
  }

  private resolve (result: any) {
    try {
        const then = this.getThen(result)
        if (then) return this.doResolve(then.bind(result), this.resolve, this.reject)
        this.fulfill(result)
    } catch (error) {
        this.reject(error)
    }
  }

  private getThen (value: Thennable) {
    const valueType = typeof value
    if (value && (valueType === 'object' || valueType === 'function')) {
      const then = value.then
      if (typeof then === 'function') return then
    }
    return null
  }

  private getHandlerType (type: ReturnType, done: boolean, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    return (value: any) => {
      if (done) return
      done = true
      return { error: onRejected, success: onFulfilled }[type](value)
    }
  }

  private doResolve (resolverFn: ExecutorFunction, onFulfilled: ResolveFunction, onRejected: RejectFunction) {
    let done = false
    try {
            resolverFn(this.getHandlerType(ReturnType.SUCCESS, done, onFulfilled, onRejected), this.getHandlerType(ReturnType.ERROR, done, onFulfilled, onRejected))
    } catch (error) {
      if (done) return
      done = true
      onRejected(error)
    }
  }

  then (onFulfilled?: ResolveFunction, onRejected?: Nulable<RejectFunction>): TypePromise {
    return new TypePromise((resolve: ResolveFunction, reject: RejectFunction) => {
      const handleResult = (type: ReturnType) => {
        return (result: any) => {
          try {
            const executorFunction = type === ReturnType.ERROR ? reject : resolve
            const checkFunction = type === ReturnType.ERROR ? onRejected : onFulfilled
            return (typeof checkFunction === 'function') ? executorFunction(checkFunction(result)) : executorFunction(result)
          } catch (error) {
            reject(error)
          }
        }
      }

      return this.done(handleResult(ReturnType.SUCCESS), handleResult(ReturnType.ERROR))
    })
  }

  private done (onFulfilled?: ResolveFunction, onRejected?: Nullable<RejectFunction>) {
    process.nextTick(() => {
      this.executeHandler({
        onFulfilled,
        onRejected
      })
    })
  }

  catch (onRejected: RejectFunction) {
    return new TypePromise((resolve: ResolveFunction, reject: RejectFunction) => {
      return this.done(resolve, (error: any) => {
        if(typeof onRejected === 'function') {
          try {
            return resolve(onRejected(error))
          } catch (error) {
            reject(error)
          }
        }
        return reject(error)
      })
    })
  }

  finally (finalFunction: Function) {
    if (typeof finalFunction === 'function') this.finalFunction = finalFunction
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们在 Typescript 中创建了自己的函数实现。这具有开发时字体的优点，但我们仍需要在函数运行时检查字体，因为构建函数时，所有代码都将透明地转换为 JavaScript。我们可以这样使用我们的诺言:

```
import { TypePromise } from './TypePromise'

function foo (param: any) {
  return new TypePromise((resolve, reject) => {
    if (Math.random() > 0.5) return setTimeout(resolve, 1000, param)
    return setTimeout(reject, 1000, 'error')
  })
}

(() => {
  foo(5)
    .then((value) => console.log(value))
    .catch((error) => console.error(error))
    .finally(() => console.log('aways return'))
})() 
```

Enter fullscreen mode Exit fullscreen mode

另请注意，在许多情况下，我们使用“`any`”作为类型，这在使用 Typescript 时并不是一个好方法，因为我们忽略了类型。所以作为一门功课，一个很好的挑战是实施通用类型，使`class TypePromise`成为`class TypePromise<T>`。我们将按照本文的顺序来处理这个更正！

如果您有兴趣了解更多信息，请查看以下参考资料:

*   [https://www.promisejs.org/implementing/](https://www.promisejs.org/implementing/)
*   [https://level up . git connected . com/understand-JavaScript-promises-by-building-a-promise-from-scratch-84c 0 FD 855720](https://levelup.gitconnected.com/understand-javascript-promises-by-building-a-promise-from-scratch-84c0fd855720)
*   [https://github.com/khaosdoctor/PromiseFromScratch](https://github.com/khaosdoctor/PromiseFromScratch)

不要忘了在我的博客上跟进我的内容，如果[订阅时事通讯](https://blog.lsantos.dev/signup?utm_source=devto&utm_medium=article&utm_campaign=promises_scratch)接收每周新闻！******