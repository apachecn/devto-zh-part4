# JavaScript 基础备忘单:范围、上下文和“这个”

> 原文：<https://dev.to/alexandrafren/a-javascript-fundamentals-cheat-sheet-scope-context-and-this-28ai>

范围

作用域指的是程序中可以访问变量的地方。一些变量可以从程序中的任何地方访问(全局范围)，而另一些则有更有限的上下文(函数和块范围)。

当我们不在一个函数或块中时(代码在两个花括号{}，我们在全局范围内。这里声明的任何变量都可以在全局范围内的任何地方访问，但也可以在函数或块内访问。

变量可以在函数作用域中声明，函数作用域存在于函数内部。这些变量对于该函数中的任何代码都是可访问的，包括附加的块，但是它们在全局范围内是不可访问的。

//该变量在全局作用域
let user = "Alex "中声明；

```
function greetUser(){ 
```

//这个变量是在函数作用域
let greeting = "Hello，"；

//这个 return 既可以访问其
作用域(函数)内声明的变量，也可以访问全局作用域变量
return greeting+user；
}

块范围由 let & const(ES6 引入的变量声明)来观察，而不是由 var 来观察。因此，在块范围内(或者在大多数情况下)声明变量时，不应该使用 var。在块范围内声明的变量(用 let 或 const)只能在该范围内访问，而不能在任何外部范围内访问。然而，块作用域可以访问全局作用域中声明的所有变量，以及任何包含函数中的变量。

块中的变量的行为与在函数中定义的一样，这意味着在该块中，变量不能被重声明，常量变量不能被重定义。但是，因为这些变量的作用域只在这个块中，所以可以在包含函数中声明同名的变量，而不一定会导致问题。
上下文

上下文指的是一个对象，当执行一个函数时它是相关的。对象具有可以使用“this”访问的属性和方法，了解函数的上下文很重要，这样我们就可以了解在执行函数后“this”的值是什么。

一般来说，函数的上下文指的是被调用的对象。对于嵌套在对象上的其他函数中的函数，以及实例化对象时使用的构造函数，都是如此。当没有在对象上调用函数时，上下文是全局的，这意味着“this”将引用窗口对象。
“这个”

上下文很重要，因为它突出显示了将被传递给“this”属性值的对象，我们可以在执行函数时使用该值。这允许我们在函数中访问对象的值，让我们编写可以被多个对象重用的函数，使我们的代码保持干燥。

当一个对象将函数存储为属性值时，“this”被隐式传递给函数，因为函数被定义为对象声明的一部分，所以函数总是知道它是如何被调用的。在下面的示例中，调用 user.greeting()会将“this”的值隐式设置为用户(对其调用函数的对象)。

const user = {
name: "Alex "，
age: 26，
//'this '值将被隐式传递给函数，因为它被定义为对象变量
greeting(){
"我的名字是${this.name}，我${this.age}岁。"；
}
}

绑定、调用和应用是在 ES5 中引入的内置函数，允许你传递一个应该用于“this”的上下文对象的参数。当调用在对象的全局范围内定义的函数时，或者当使用内置的 JavaScript 方法(如 reduce、filter、map 等)时，它们很有帮助。)并需要显式声明上下文，以便函数不会返回 TypeError。

函数 greeting(){
console . log(`Hello, ${this.name}!`)；
}

const user = {
name:'亚历克斯'
}

//通过 call 传递对象，确保 greeting 函数将通过' this'
greet.call(user)访问正确的对象；

构造函数创建一个对象，然后使用新实例化的对象作为“this”值，允许它为属性设置值。

class Game {
//构造函数内的上下文是实例化的对象，允许
us 用‘this’访问新对象，并设置其属性值
构造函数(id，name，release_year，consoles，review = " "){
this . id = id；
this . name = name；
this.release_year =发布 _ 年；
this.consoles =控制台；
}
}

ES6 中引入的箭头函数没有隐含的“this ”,可以用在我们可能需要绑定、调用或应用方法的地方。它们将查看它们的包含函数或父函数来确定“this”的值。

定义“this”的最后一种方式是在全局范围内编写函数，但使用“this ”,并且在调用函数时不使用 bind、call 或 apply。在这种情况下,“this”的值将是窗口对象，并将作为未定义的返回。

* *本文最初发布在我的博客上，地址为[http://alexandrafren . com/2019/02/28/a-JavaScript-fundamentals-cheat-sheet-scope-context-and-This/](http://alexandrafren.com/2019/02/28/a-javascript-fundamentals-cheat-sheet-scope-context-and-this/)