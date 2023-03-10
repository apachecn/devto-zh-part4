# ES6 基础:Var、Let 和 Const 之间的区别

> 原文：<https://dev.to/travissanon/es6-basics-the-difference-between-var-let-and-const-4a5e>

## 差异

**`var`**-***【var】**的作用范围仅限于其定义的“函数”。如果它是在任何函数之外定义的，那么变量的范围就是全局范围。*

*   **词法范围:**功能
*   可以重新分配
*   **初始化:** `undefined`

**`let`**-***【让】**的范围仅限于花括号定义的“块”。*

*   **词法范围:**块
*   可以重新分配
*   **初始化:**无

**`const`**-***【常数】**的范围与**【让】**相同。此外，用 const 定义的变量不能重新赋值。*

*   **词法范围:**块
*   不能重新赋值(***会不会变异*****)**
***   **初始化:**无**

 **## 变量声明

您可以有意在特定范围内声明变量。

我们可以在**全球范围内** :
进行

```
var x
console.log(x) // undefined

// (When a `var` gets declared without a value, it defaults to `undefined`) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在**功能范围** :
内进行

```
function marco() {
  var x
  console.log(x)
}

marco() // undefined 
```

Enter fullscreen mode Exit fullscreen mode

而且我们可以在**块范围** :
内做

```
function marco() {
  if (true) {
    var x
    console.log(x)
  }
}

marco() // undefined 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在全局作用域和函数作用域中都声明了`var x`,一切都很好。

```
var x = "outside"

function marco() {
  var x = "inside"
  console.log(x) // "inside"
}

marco()
console.log(x) // "outside" 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们把函数内部的`var x`声明改成了重新赋值呢？

```
var x = "outside"

function marco() {
  x = "inside"
  console.log(x) // "inside"
}

marco()
console.log(x) // "inside" 
```

Enter fullscreen mode Exit fullscreen mode

哎呀。

如果我们从内部变量中移除`var`，函数外部的`var x`将被函数内部的重新赋值所覆盖。我们需要指定`x`的作用域应该只在函数`marco()`中，否则就会发生这种情况。

## 功能范围

在 JavaScript 中，通过在函数中定义变量来限制变量的范围。这叫做**函数作用域**。

```
function marco() {
  var text = "it works"
  console.log(text) // "it works"
}

marco()
console.log(text) // nothing 🤷‍♀ 
```

Enter fullscreen mode Exit fullscreen mode

因为`var`是函数作用域，所以当它在函数中时，它只考虑它的作用域。因此，这意味着下面的也将工作:

```
function marco() {
  var text = "it works"
  if (true) {
    var text = "no it doesn't"
  }
  console.log(text) // no it doesn't
}

marco() 
```

Enter fullscreen mode Exit fullscreen mode

那不好。

我在函数内部创建了一个`if`语句，添加了一个与前一个同名的`var`,并意外地重新分配了它的值。一个显而易见的解决方案是使用不同的变量名，但是让我们使用一个更好的解决方案来完全避免这种情况。

## 封锁范围

与 **var** 不同， **let** & **const** 都是块作用域。

这意味着只要变量是在一组花括号内创建的，它的作用域就会被限制在这些花括号内的代码块内。这适用于函数、for 循环、if 语句、switch 语句等。

```
function marco() {
  let text = "it works"
  if (true) {
    let text = "let is neat!"
    console.log(text) // let is neat!
  }
  console.log(text) // it works
}

marco() 
```

Enter fullscreen mode Exit fullscreen mode

嘿，那很好。我们可以毫无问题地在一个函数块中创建多个变量。没有意外的重新分配，没有混乱。不过，您仍然应该练习正确的命名约定。如果两个变量在同一个函数范围内，尽量不要用同一个名字命名它们。

## 不得转任

**const** 会**不会**让你重新赋值变量。

```
const marco = "polo"

marco = "solo" // Hey, thats illegal 
```

Enter fullscreen mode Exit fullscreen mode

尽管它不是完全不可改变的。你**可以改变用`const`声明的**变量。

### 阵列突变

```
const arr = [1]

arr.push(2)

console.log(arr) // [1, 2] 
```

Enter fullscreen mode Exit fullscreen mode

### 物体突变

```
const obj = {
  saying: "marco",
}

obj.saying = "polo"

console.log(obj.saying) // polo 
```

Enter fullscreen mode Exit fullscreen mode

## 让&常量提升

三类变量 *(var，let，const)* **全部**提升。*提升*意味着在代码执行之前，变量被移动到它们作用域的顶部。

#### 注:所有变量[都被“吊起”](https://stackoverflow.com/questions/31219420/are-variables-declared-with-let-or-const-not-hoisted-in-es6)

不像`var`，`let`和`const`声明是**而不是**初始化成任何东西。

`var`被初始化为`undefined`。

```
console.log(a) // undefined
console.log(b) // Error: That's Illegal

var a = 0
let b = 0 
```

Enter fullscreen mode Exit fullscreen mode

请注意，日志没有输出我们声明的变量中的内容。解释器是这样执行的:

```
var a
let b

console.log(a) // undefined
console.log(b) // Error: That's Illegal

a = 0
b = 0 
```

Enter fullscreen mode Exit fullscreen mode

解释器将变量声明提升到了作用域的顶部，但是变量还没有被分配到`0`直到解释器到达那个点。

使用`let`和`const`很好，因为如果你在声明变量之前调用它，你会得到一个清晰的`Uncaught ReferenceError: b is not defined`，而不是一个无声的`undefined`。

保持饥饿，保持愚蠢🙏**