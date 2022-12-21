# 为了更好的可重用性，组合你的函数

> 原文：<https://dev.to/aminnairi/compose-your-function-for-a-better-reusability-3ck7>

今天我们将学习用 JavaScript 实现一个 curry 函数。这个函数将帮助我们创建更多的可重用函数。

## 什么是阿谀奉承？

假设我们在真实的环境中。你的任务是写一个函数，用一种特殊的方式打招呼。第一个参数是可以添加在消息前的前缀。第二个是要问候的人的姓，第三个是名字。

```
function greet(prefix, lastname, firstname) {
  return `${prefix}  ${lastname}, ${firstname}`
}

console.log(greet("Hello", "ALLEN", "Barry"))
console.log(greet("Hello", "ALLEN", "Nora"))
console.log(greet("Hello", "ALLEN", "Henry"))
console.log(greet("Hello", "ALLEN", "Bart"))

// Hello ALLEN, Barry
// Hello ALLEN, Nora
// Hello ALLEN, Henry
// Hello ALLEN, Bart 
```

Enter fullscreen mode Exit fullscreen mode

但是我们不满意，因为有很多重复。尤其是在我们的代码中重复出现的前缀和姓氏。让我们解决这个问题。

```
function greet(prefix, lastname, firstname) {
  return `${prefix}  ${lastname}, ${firstname}`
}

const prefix = "Hello"
const lastname = "ALLEN"

console.log(greet(prefix, lastname, "Barry"))
console.log(greet(prefix, lastname, "Nora"))
console.log(greet(prefix, lastname, "Henry"))
console.log(greet(prefix, lastname, "Bart"))

// Hello ALLEN, Barry
// Hello ALLEN, Nora
// Hello ALLEN, Henry
// Hello ALLEN, Bart 
```

Enter fullscreen mode Exit fullscreen mode

那更好！但是你觉得自己重复了很多遍。我们可以做什么来防止在 greet 函数的调用中重复前缀和姓氏？我们可以这样写。

```
function greet(prefix, lastname) {
  return function(firstname) {
    return `${prefix}  ${lastname}, ${firstname}`
  }
}

const prefix = "Hello"
const lastname = "ALLEN"
const greetAllens = greet(prefix, lastname)

console.log(greetAllens("Barry"))
console.log(greetAllens("Nora"))
console.log(greetAllens("Henry"))
console.log(greetAllens("Bart"))

// Hello ALLEN, Barry
// Hello ALLEN, Nora
// Hello ALLEN, Henry
// Hello ALLEN, Bart 
```

Enter fullscreen mode Exit fullscreen mode

太好了！这更像是我们写的一个可重用的函数。我们所做的是通过使用一个函数作为返回值来推迟最终的返回。这被称为结束。它将记住其先前的上下文(变量`prefix` & `lastname`)以便在下一次调用中使用它们，这将有效地返回格式化的字符串。但是我们想问候更多的人。

```
function greet(prefix, lastname) {
  return function(firstname) {
    return `${prefix}  ${lastname}, ${firstname}`
  }
}

const prefix = "Hello"
const lastname = "ALLEN"
const greetAllens = greet(prefix, lastname)

console.log(greetAllens("Barry"))
console.log(greetAllens("Nora"))
console.log(greetAllens("Henry"))
console.log(greetAllens("Bart"))
console.log(greet("Hello", "QUEEN")("Oliver"))

// Hello ALLEN, Barry
// Hello ALLEN, Nora
// Hello ALLEN, Henry
// Hello ALLEN, Bart
// Hello QUEEN, Oliver 
```

Enter fullscreen mode Exit fullscreen mode

我们不得不两次调用函数来问候奥利弗·奎因。有效果，但是感觉不自然。如果我们想要另一个前缀消息呢？我们必须相应地更新我们的功能。

```
function greet(prefix) {
  return function(lastname) {
    return function(firstname) {
      return `${prefix}  ${lastname}, ${firstname}`
    }
  }
}

const greetAllens = greet("Hello")("ALLEN")
const greetQueens = greet("Welcome")("QUEEN")

console.log(greetAllens("Barry"))
console.log(greetAllens("Nora"))
console.log(greetAllens("Henry"))
console.log(greetAllens("Bart"))
console.log(greetQueens("Oliver"))
console.log(greetQueens("Robert"))
console.log(greetQueens("Moira"))

// Hello ALLEN, Barry
// Hello ALLEN, Nora
// Hello ALLEN, Henry
// Hello ALLEN, Bart
// Welcome QUEEN, Oliver
// Welcome QUEEN, Robert
// Welcome QUEEN, Moira 
```

Enter fullscreen mode Exit fullscreen mode

但话说回来，有些事不对劲。感觉一点也不自然。如果我们想问候一个单身的人呢？

```
greet("HI")("DOE")("Jhon") 
```

Enter fullscreen mode Exit fullscreen mode

现在很清楚，我们需要找到一个对这两种情况都适用的解决方案:要么我有一个我想部分调用的函数，要么用正确数量的参数调用它。这叫阿谀奉承。

## 如何在 JavaScript 中实现 currying？

我将要展示的是我在 JavaScript 中定义 curry 函数的方法。我很确定有其他的变体可以达到这个目的，但是重要的是理解这个定义背后的思想。

下面是我们将如何使用我们的函数:

```
const greet = curry(function(prefix, lastname, firstname) {
      return `${prefix}  ${lastname}, ${firstname}`
})

const greetAllens = greet("Hello", "ALLEN")
const greetQueens = greet("Welcome", "QUEEN")

console.log(greetAllens("Barry"))
console.log(greetQueens("Oliver"))
console.log(greet("Hi", "DOE", "Jhon"))

// Hello ALLEN, Barry
// Welcome QUEEN, Oliver
// Hi DOE, Jhon 
```

Enter fullscreen mode Exit fullscreen mode

我们开始吧。我们现在有了一个函数，可以像普通函数一样调用，也可以被 curried。现在让我们看看如何用 JavaScript 实现这一点。

## 实现

正如我们在上面的例子中看到的，curry 函数将包装我们的函数定义。显然，这是对我们的一个暗示，因为它肯定会将一个函数(回调)作为其参数，并返回一个函数(闭包)。稍后我们将看到闭包的内部逻辑。让我们从我们所知道的开始。

```
function curry(callback) {
  return function() {
    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
 callback
                      |
                      |
                      v
const greet = curry(function(prefix, lastname, firstname) {
      return `${prefix} ${lastname}, ${firstname}`
}) 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但这还不够。首先。让我们来看看这个 curried 函数的常规调用。我们需要找到一种方法来收集将传递给函数的所有参数，但是我们不知道将传递多少个参数。所以我们将使用析构操作符来收集数组中的参数。我们将使用 spread 操作符，并在调用闭包时调用回调函数及其所有参数。

```
function curry(callback) {
  return function(...parameters) {
    return callback(...parameters)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
 parameters
            /\
           /  \
          /    \
         /      \
        /        \
        |         |
        |         |
        V         V
greet("Hello", "ALLEN") 
```

Enter fullscreen mode Exit fullscreen mode

这已经适用于这种情况:

```
function curry(callback) {
  return function(...parameters) {
    return callback(...parameters)
  }
}

const greet = curry(function(prefix, lastname, firstname) {
      return `${prefix}  ${lastname}, ${firstname}`
})

console.log(greet("Hi", "DOE", "Jhon")) 
```

Enter fullscreen mode Exit fullscreen mode

但是当我们试图重用部分应用程序 greet 函数时，它就不起作用了。我们需要添加更多的代码来使它在这两种情况下都能工作。

我要用的是递归。这个想法是，只要在我们的闭包中传递的参数数量不够，我将返回另一个 curried 函数，并继续在一个数组中收集参数。当我们最终获得必要数量的参数时，我们将能够使用所有需要的参数来调用回调。

让我们先试着检查一下什么时候返回回调，什么时候返回一个 curried 函数。

```
function curry(callback) {
  return function(...parameters) {
    if (parameters.length >= callback.length) {
      return callback(...parameters)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我说如果传递的参数足够满足函数签名，我们就用它所有的参数调用函数。这并没有改变到目前为止所做的事情。显然，有趣的是当我们没有必要的参数时会发生什么。

```
function curry(callback) {
  return function(...parameters) {
    if (parameters.length >= callback.length) {
      return callback(...parameters)
    }

    return curry(callback)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了核心思想。我们简单地在回调函数中递归调用 curry 函数，直到有足够的参数。但是如果你仔细看，我们在这里做的是不正确的。当我们试图提供比所需更少的参数时，函数将简单地调用 curry 函数。但是，我们还没有在对`curry`的下一次调用中存储我们的参数。我们永远无法通过这种方式收集参数。我们需要在递归调用中传递第二个参数。

```
function curry(callback, ...oldParameters) {
  return function(...parameters) {
    if (parameters.length >= callback.length) {
      return callback(...parameters)
    }

    return curry(callback, ...parameters)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了，我们就快成功了，但还缺少一点点。看这里，我们现在有了之前传递的参数。但是我们什么也没做。这里的想法是将前面传递的参数与后面的参数合并。我们现在要做的是将旧参数和提供的参数联合起来。

```
function curry(callback, ...oldParameters) {
  return function(...parameters) {
    const nextParameters = [...oldParameters, ...parameters]

    if (nextParameters.length >= callback.length) {
      return callback(...nextParameters)
    }

    return curry(callback, ...nextParameters)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
 parameters   nextParameters
             /\            |
            /  \           |
           /    \          |
          /      \         | 
         /        \        |
         |        |        |
         v        v        v
greet("Hello", "ALLEN")("Barry") 
```

Enter fullscreen mode Exit fullscreen mode

```
 parameters   
                                 /\     
                                /  \   
                               /    \ 
                              /      \ 
                             /        \
                             |        |
                             v        v
const greetAllens = greet("Hello", "ALLEN")

           nextParameters
               |
               |
               v
greetAllens("Barry") 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个有效的库里函数。我们可以在任何函数上使用它。

```
function curry(callback, ...oldParameters) {
  return function(...parameters) {
    const nextParameters = [...oldParameters, ...parameters]

    if (nextParameters.length >= callback.length) {
      return callback(...nextParameters)
    }

    return curry(callback, ...nextParameters)
  }
}

const add = curry((a, b) => a + b)
const increment = add(1)
const decrement = add(-1)

console.log(add(1, 2)) // 3
console.log(increment(1)) // 2
console.log(decrement(1)) // 0 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

有一些库可以负责 curry 函数的实现，比如 Underscore.js 或 Lodash。我向您展示的是一个可以根据需求或您自己的规范而变化的实现示例。

这不是一个简单的话题，我可能没有解释清楚。请，如果你有任何问题(没有愚蠢的问题)，请在评论中问我，我很乐意回答他们。

感谢您的阅读！