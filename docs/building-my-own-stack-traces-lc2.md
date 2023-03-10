# 构建我自己的堆栈跟踪

> 原文：<https://dev.to/staab/building-my-own-stack-traces-lc2>

你在 javascript 项目中遇到过错误吗？

```
TypeError: Cannot read property 'subdomain' of undefined
      at /Users/jstaab/my-project/src/server/db/core.js:568:35
      at Array.filter (<anonymous>)
      at /Users/jstaab/my-project/src/server/db/core.js:147:1
      at log (src/common/logger.js:75:14)
      at _callee16$ (src/server/db/core.js:768:28)
      at tryCatch (node_modules/regenerator-runtime/runtime.js:45:40)
      at Generator.invoke [as _invoke] (node_modules/regenerator-runtime/runtime.js:271:22)
      at Generator.prototype.(anonymous function) [as next] (node_modules/regenerator-runtime/runtime.js:97:21)
      at asyncGeneratorStep (src/server/db/core.js:22:103)
      at _next (src/server/db/core.js:24:194)
      at <anonymous>
      at process._tickCallback (internal/process/next_tick.js:189:7) 
```

Enter fullscreen mode Exit fullscreen mode

即使有了源地图，堆栈也只会指向错误被抛出的地方，但是我不知道在应用程序的什么地方调用了那个东西。

今天我决定做点什么，通过实现我自己的堆栈跟踪。比如上面的错误，我得到了这个额外的信息:

```
Intercepted in:
     server/db/core/getWhereColumns
     server/db/core/deleteRecords
     server/db/domain/MyModel.delete 
```

Enter fullscreen mode Exit fullscreen mode

这只是被追加到错误的内置堆栈跟踪中(它首先只是字符串)。这使得我隔离问题并修复它们的速度快了几个数量级，无论我是在本地开发还是在生产中诊断问题。

这种方法有缺点:有一些讨厌的样板文件，并且肯定有一些性能下降，我将在这篇文章的结尾进行讨论。但是值得吗？我也这么认为

# 实现

基本概念是，您有一个 decorator，它捕捉错误和拒绝的承诺，注释它们的堆栈，并重新抛出它们。

```
const IS_INSTRUMENTED = Symbol('isInstrumented')

const instrument = (name, f) => {
  if (f[IS_INSTRUMENTED]) {
    return f
  }

  function wrapper(...args) {
    let result
    try {
      result = f.call(this, ...args)
    } catch (error) {
      throw modifyStack(name, error)
    }

    if (result instanceof Promise) {
      result = result.catch(error => {
        throw modifyStack(name, error)
      })
    }

    return result
  }

  wrapper.name = name
  wrapper[IS_INSTRUMENTED] = true

  return wrapper
} 
```

Enter fullscreen mode Exit fullscreen mode

我在这里不使用异步函数的原因是，我希望能够检测同步函数，而不用将它们转换成承诺返回函数(异步函数*总是*返回承诺)。我们还使用了 es5 `function`语法和`bind`以便我们可以传递`this`。

这个函数接受一个名称和另一个函数，并返回一个将参数传递给修饰函数的函数，捕捉它可能抛出的任何同步错误并修改它们的堆栈。它还检查返回值是否是一个承诺，并修改返回值的堆栈。

> 请注意，您应该只抛出/拒绝`Error` s，因为这将向任何其他东西添加不可靠的`stack`属性。

这里是`modifyStack` :

```
const modifyStack = (name, error) => {
  if (!error.originalStack) {
    error.originalStack = error.stack
    error.stack += "\n\n Intercepted in:"
  }

  error.stack += `\n\t ${name}`

  return error
} 
```

Enter fullscreen mode Exit fullscreen mode

## 一个基本例子

下面是你如何使用`instrument` :

```
const concat = instrument('utils.concat', (a, b) => a.concat(b))
const append = instrument('utils.append', (a, b) => concat(a, [b])) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我*不建议*修饰实用函数，因为它们可能是调用图中的叶节点，并且通常是同步调用的，因此您可以获得很好的堆栈跟踪。您也不希望将这个装饰器的开销添加到经常被调用的东西中。这个装饰器对于控制器、强大的逻辑函数等来说更好，可以让你知道什么代码路径出错了。考虑到这一点，如果用奇怪的参数调用上面的函数会怎么样呢？

```
append(1, 2)

/*
Uncaught TypeError: a.concat is not a function
    at <anonymous>:1:55
    at wrapper (<anonymous>:7:18)
    at <anonymous>:2:53
    at wrapper (<anonymous>:7:18)
    at <anonymous>:1:1

    Intercepted in:
     utils.concat
     utils.append
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这不是很有帮助，但是请记住，这只是一个小例子。在复杂的应用程序代码中，这类事情可能是救命稻草。

# 类方法

类语法会让这种方法变得很麻烦。如果您不想添加语法转换器，您还有另一个选择:检测整个类。看起来是这样的:

```
const instrumentClass = cls => {
  let obj = cls.prototype
  do {
    if (obj.constructor === Object) {
      break
    }

    Object.getOwnPropertyNames(obj).forEach(k => {
      if (typeof obj[k] === 'function' && k !== 'constructor') {
        obj[k] = instrument(`${obj.constructor.name}.${k}`, obj[k])
      }
    })
  } while (obj = Object.getPrototypeOf(obj))
}

class Libation {
  pourOn(target) {
    target.wet = true
  }
}

class Tea extends Libation {
  async pourOn(target) {
    await new Promise(() => super.pourOn(target))

    target.bland = true
  }
}

instrumentClass(Tea) 
```

Enter fullscreen mode Exit fullscreen mode

我们基本上是按照这个 [StackOverflow 答案](https://stackoverflow.com/a/31055217/1467342)来遍历继承链，这样我们就可以枚举所有附加到我们实例的方法。然后我们检测这些方法，用类名和方法名标记它们，跳过`constructor`。

下面是它在实践中的样子:

```
const tea = new Tea()
const ground = null

tea.pourOn(ground)

/*
Uncaught (in promise) TypeError: Cannot set property 'wet' of null
    at Tea.pourOn (<anonymous>:57:16)
    at Tea.wrapper (<anonymous>:5:18)
    at target.bland (<anonymous>:63:35)
    at new Promise (<anonymous>)
    at Tea.pourOn (<anonymous>:63:11)
    at Tea.wrapper (<anonymous>:5:18)
    at <anonymous>:74:5

    Intercepted in:
     Libation.pourOn
     Tea.pourOn
*/ 
```

Enter fullscreen mode Exit fullscreen mode

请注意，最初的堆栈跟踪是完全错误的——这是在 Chrome 的 devtools 中运行的，没有构建步骤。但是现在我们有了更多的信息:错误发生在被 Tea.pourOn 调用的 Libation.pourOn！我不知道你怎么想，但我觉得这很有帮助。

# 脚枪和表演

显然，任何时候你改变你的宿主语言的语义，你都需要小心。这段代码还不完美，但我已经尝试解决了一些可能的问题:

*   这段代码不会将同步函数包装在承诺中。
*   这段代码将把`this`和参数一起传递给被包装的函数。
*   `instrumentClass`*是否为每个方法创建一个新的副本，包括一个包装闭包。它*没有*做的是为每个实例创建一个新的副本，这意味着我们仍然拥有面向对象方法分派的一般内存节省特性。*
**   我们没有必要包装`Object`的每个方法，我们可以很容易地将代码添加到我们包装的方法的白名单中。*

 *性能也不可怕。下面是一小段分析代码:

```
const profile = f => {
  now = new Date().valueOf()
  for (let i = 0; i < 10000000; i++) f(i, i)
  console.log(new Date().valueOf() - now)
}

profile((x, y) => x + y)
profile(instrument('hi there', (x, y) => x + y)) 
```

Enter fullscreen mode Exit fullscreen mode

如果没有包装器，这大约需要 200 毫秒。有了包装器，它出来约 450 毫秒。这慢了 150%，但这确实是最坏的情况，因为包装器比内部函数做更多的工作(这就是为什么包装经常使用的实用函数不是一个好主意)。如果内部函数做更多的工作，那么包装器的成本可以忽略不计:

```
const profile = f => {
  now = new Date().valueOf()
  for (let i = 0; i < 1000; i++) f(i, i)
  console.log(new Date().valueOf() - now)
}

const f = (x, y) => {
  const r = []
  for (let i = 0; i < x; i++) {
    for (let j = 0; j < x; j++) {
      r.push(i + j)
    }
  }

  return r
}

profile(f)
profile(instrument('hi there', f)) 
```

Enter fullscreen mode Exit fullscreen mode

这两个版本的时间总计约为 11200 毫秒。

# 结论

这段代码可以在 github 上找到——请在评论中告诉我你对这种方法的看法！我是一个留着大胡子的大胆的特立独行者，还是会像罗德·金布尔一样，在走向一辆货车的时候拼命喊着“威士忌”？

感谢阅读！*