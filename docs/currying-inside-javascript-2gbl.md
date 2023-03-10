# JavaScript 中的 Currying

> 原文：<https://dev.to/jsmanifest/currying-inside-javascript-2gbl>

在[媒体](https://medium.com/@jsmanifest)上找到我

Currying 是一种处理函数的高级技术，在多种编程语言中都有使用。

当你把一个带多个参数的函数分解成一系列嵌套函数的时候，你就有了一个 T4 咖喱。每个嵌套函数都应该有下一个参数。

curry 函数每次都会返回一个新的函数,直到每次调用都收到所有的参数。这些参数能够在 currying 通过*闭包*的整个生命周期中存在，并将全部用于执行最终的函数。

一个非常基本的例子可以是这样的:

```
function combineWords(word) {
  return function(anotherWord) {
    return function(andAnotherWord) {
      return `${word}  ${anotherWord}  ${andAnotherWord}`
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

要使用它，您可以多次调用该函数，直到它到达最后一个函数:

```
const result = combineWords('hello,')('good')('morning')
console.log(result)

// result: 'hello, good morning' 
```

Enter fullscreen mode Exit fullscreen mode

所以现在发生的是`combineWords`是一个固化的函数(很明显),在它执行系列中的下一个函数之前等待一个单词。您可以将`'wow!'`到`combineWords`绑定到一个变量，并重用它来创建其他以`'wow!'` :
开头的问候语

```
let greet = combineWords('wow!')
greet = greet('nice')

console.log(greet('jacket'))
console.log(greet('shoes'))
console.log(greet('eyes'))
console.log(greet('socks'))
console.log(greet('hat'))
console.log(greet('glasses'))
console.log(greet('finger nails'))
console.log(greet('PS3'))
console.log(greet('pet'))

/*
result:
  "wow! nice jacket"
  "wow! nice shoes"
  "wow! nice eyes"
  "wow! nice socks"
  "wow! nice hat"
  "wow! nice glasses"
  "wow! nice finger nails"
  "wow! nice PS3"
  "wow! nice pet"
*/ 
```

Enter fullscreen mode Exit fullscreen mode

如果这个概念有点难以理解，试着这样理解:

> 母亲在烹饪前期待所有的 4 个鸡蛋(争论),她的 4 个孩子将每人拿一个给她，一次一个。

```
function Egg() {...}

// the curry func
function prepareCooking(cook) {
  return function(egg1) {
    return function(egg2) {
      return function(egg3) {
        return function(egg4) {
          return cook(egg1, egg2, egg3, egg4)
        }
      }
    }
  }
}

const cook = function(...eggs) {
  api.turnOnStove()
  api.putEggsOnTop(...eggs)
  api.pourSalt()
  api.serve()
  console.log('served children')
  return 'served'
}

const start = prepareCooking(cook)

let collect = start(new Egg())
collect = collect(new Egg())
collect = collect(new Egg())
collect = collect(new Egg()) // this steps into the last function witih argument "egg4" which will invoke the callback passed to "prepareCooking"

// result:  console.log --> "served children"
// collect === 'served' 
```

Enter fullscreen mode Exit fullscreen mode

为了调用`cook`回调，需要一个接一个地传递所有 4 个鸡蛋，每个鸡蛋都预先填充了等待调用的下一个函数。

如果你停在第三个蛋上:

```
let collect = start(new Egg())
collect = collect(new Egg())
collect = collect(new Egg()) 
```

Enter fullscreen mode Exit fullscreen mode

那么由于最后一个期望`egg4`的函数还没有到达，`collect`的值就是那个函数:

```
function prepareCooking(cook) {
  return function(egg1) {
    return function(egg2) {
      return function(egg3) {
        // HERE
        return function(egg4) {
          return cook(egg1, egg2, egg3, egg4)
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了完成咖喱，收集最后一个鸡蛋:

```
let collect = start(new Egg())
collect = collect(new Egg())
collect = collect(new Egg())
collect = collect(new Egg())

// collect === 'served' 
```

Enter fullscreen mode Exit fullscreen mode

现在，重要的是要知道每个嵌套函数都可以访问 curry 函数的外部作用域。了解了这一点，就可以在每个嵌套函数之间提供定制的逻辑，以适应特定的情况。但是最好留下一个咖喱作为咖喱，不要其他的。

一个更高级的 curry 函数可以如下所示:(我将提供一个`ES5`版本和一个`ES6`版本，因为有很多展示 ES5 语法的旧教程，对于新的 JavaScript 开发人员来说可能有点难懂)

> ES5

```
function curry(fn) {
  return function curried() {
    const args = Array.prototype.slice.call(arguments)
    const done = args.length >= fn.length
    if (done) {
      return fn.apply(this, args)
    } else {
      return function() {
        const args2 = Array.prototype.slice.call(arguments)
        return curried.apply(this, args.concat(args2))
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

...与以下内容相同:

> ES6

```
const curry = (fn) => {
  return function curried(...args) {
    const done = args.length >= fn.length
    if (done) {
      return fn.apply(this, args)
    } else {
      return (...args2) => curried.apply(this, [...args, ...args2])
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们更详细地解释一下这个例子:

当您调用`curry(fn)`时，它将返回内部的`curried`函数，该函数将在调用时等待下一个参数。当你调用这个内部函数时，它会计算两个条件:

1.  调用者传入的参数是否足够满足`fn`的所有参数？
2.  还是仍然缺少`fn`需要的参数？

如果*第一个*是这种情况，那么`fn`声明了我们需要的所有参数，库里将通过返回对`fn`的调用并将所有收到的参数传递给它(现在基本上正常调用`fn`)来结束

然而，如果*数字 2* 是这种情况，那么咖喱必须继续下去，我们必须以某种方式回到内部`curried`函数，以便我们可以继续接收更多的参数，直到它满足`fn`的参数。代码`return (...args2) => curried.apply(this, [...args, ...args2])`累积了到目前为止暴露的所有参数，并使用它们继续这种情况下的搜索。

有一条重要的规则:

> 在等待收集所有参数之前要调用的函数必须有固定数量的参数。这意味着函数不能扩展参数(例如:`fn(...args)`)

例:

```
const curry = (fn) => {
  return function curried(...args) {
    const done = args.length >= fn.length
    if (done) {
      return fn.apply(this, args)
    } else {
      return (...args2) => curried.apply(this, [...args, ...args2])
    }
  }
}

// This is invalid because it uses ...args.  The curry does not understand where to stop
function func(...args) {
  //
}

const currying = curry(func) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我认为咖喱是一个有趣的技术，因为创造一个咖喱涉及到其他先进的技术。这里涉及到闭包、高阶函数和递归。

这篇文章到此结束。我希望你找到了一些有价值的东西，并在未来寻找更多！

在[媒体](https://medium.com/@jsmanifest)上找到我