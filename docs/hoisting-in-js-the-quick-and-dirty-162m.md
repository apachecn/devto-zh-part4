# JS 中的吊装:又快又脏

> 原文：<https://dev.to/bbarbour/hoisting-in-js-the-quick-and-dirty-162m>

在阅读这篇文章之前，先看看: [Var 和函数作用域](https://dev.to/steelvoltage/var-and-function-scope-in-javascript-3k4p)

```
console.log(hoistAndRun()) // returns 'also hoisted and can be ran'
console.log(hoistMe) // returns undefined

var hoistMe = 'hoisted, but undefined'

function hoistAndRun() {
  return 'also hoisted and can be ran'
} 
```

Enter fullscreen mode Exit fullscreen mode

关于提升，首先要理解的是，它是 Javascript 的一个特性。(我觉得其他一些语言也有，比如 Python，但并不是都用。)你会看到很多人把它解释为变量(用`var`声明)和函数(用`function`关键字)被“提升”到代码顶部。

这不准确。Javascript 不会这样做，至少在物理上不会。这更像是一种类比或概念性的思考方式。相反，Javascript 在内存中为变量和函数创建空间。可以把它看作是预订晚餐的编程等价物。

被提升的`var`变量将返回`undefined`。因为，你还没有给它们赋值(直到你真正用`=`赋值)。函数可以立即运行，因为在代码运行之前，它们已经完全放在内存中了。

使用`var`的函数表达式*被*提升，但不能马上调用。

```
console.log(hoistAndRunAgain()) // TypeError: hoistAndRunAgain is not a function

var hoistAndRunAgain = function() {
  return 'hoisted, but undefined as well'
} 
```

Enter fullscreen mode Exit fullscreen mode

用`const`和`let`赋值的变量和函数表达式不会被提升，因为它们使用了块范围。

```
console.log(hoistConstant) // ReferenceError: hoistContant is not defined
console.log(hoistLet) //ReferenceError: hoistLet is not defined

const hoistConstant = 'not hoisted'
let hoistLet = 'not hoisted' 
```

Enter fullscreen mode Exit fullscreen mode

据我所知，这是快速和肮脏的提升。关于吊装可能有更多的细微差别和细节可以提供，但希望这足以让你开始，或至少唤起你的记忆。