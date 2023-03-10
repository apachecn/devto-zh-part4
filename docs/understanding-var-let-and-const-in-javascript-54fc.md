# 理解 JavaScript 中的 var、let 和 const

> 原文：<https://dev.to/contactshadab/understanding-var-let-and-const-in-javascript-54fc>

> 你可以在 [CosmoCode.io](//CosmoCode.io) 查看我关于前端开发、Selenium 和测试自动化的其他文章

在本教程中，我们将探索在 JavaScript 中声明变量的三种不同方式——关键字`var`、`let`和`const`。关键字`var`从 JavaScript 开始就有了。ES6 新增`let`和`const`关键词。我们也会明白为什么`var`会有问题，我们应该用`let`或`const`来代替。

## `var`关键词

直到 ES5 `var`是 JavaScript 中声明变量的唯一方式:

```
//Declaring variable
var car;
//Initializing variable
car = 'Tesla'; 
```

Enter fullscreen mode Exit fullscreen mode

大多数时候我们同时进行声明和初始化:

```
var car = 'Tesla'; 
```

Enter fullscreen mode Exit fullscreen mode

嘿，我知道这些简单的事情。它们在所有编程语言中都很常见。

不，它们没有那么简单。在 JavaScript 中，作用域的工作方式不同，这给初学者带来了很大的困惑和挫折..还有*吊*和*蔽*的概念。

如果你对“范围”、“提升”或“遮蔽”没有概念，不要惊慌，继续阅读。

### JavaScript 中变量的作用域

编程界的术语*作用域*指的是变量或其他实体的*可见性*。变量在声明它们的范围之外是不可见的。在 JavaScript 中(直到 ES5)，变量的作用域要么是*全局* *作用域*，要么是*函数* *作用域*。在 ES6 之前，没有*块范围*的概念。

#### 全局范围

任何在函数外部声明的变量都是全局范围的，这意味着它们可以被程序的任何部分访问。

让我们看看实际情况:

```
var msg = 'Hello World';
function greet(){
  console.log(msg);
}

greet(); 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Hello World 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，变量`msg`被定义在任何函数之外。所以它有一个全球范围。我们知道，如果任何变量具有全局范围，程序的任何部分都可以访问它。因此它在函数`greet()`中是可见的。

#### 局部作用域(函数作用域)

如果一个变量在函数内被声明为*，那么它在函数*外*是不可见的。即使变量是在代码块*(例如`for`、`while`、`if`)内声明的(使用`var` ) *，变量在整个函数中是可见的，在代码块*外的*中也是可见的。*

让我们举个例子:

```
function greet(){
  if(true){
    var msg = 'Hello World';
  }
  console.log(msg);
}

greet();
console.log(msg); 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Hello World
Uncaught ReferenceError: msg is not defined 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到变量的范围`msg`是*函数的范围*T5。尽管变量在`if`块的内被声明为*，但它在函数内的*局部块外*也是可用的。但是它在函数*之外*是不可用的，因为我们可以看到试图在函数之外访问它的输出`Uncaught ReferenceError: msg is not defined`。*

这是由于吊装造成的。

### 吊装

你在哪里升国旗？

在城堡的顶部。

让我们举个例子来理解 JavaScript 中的*提升*:

```
console.log(msg);
var msg = 'Hello World'; 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
undefined 
```

Enter fullscreen mode Exit fullscreen mode

等等…！我想我们应该得到下面的错误，因为当我们试图访问变量 msg 时没有声明它

```
Uncaught ReferenceError: msg is not defined 
```

Enter fullscreen mode Exit fullscreen mode

在 JavaScript 中，变量在特定范围内的什么地方声明并不重要。所有变量声明都被移动到其作用域的顶部。

上面的程序被 JS 解释器翻译成:

```
var msg;
console.log(msg);
msg = 'Hello World'; 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，变量声明被挂在作用域的顶部，在本例中是全局作用域。但是变量赋值没有被提升。JavaScript 解释器赋予它默认值`undefined`。因此我们得到了输出`undefined`。

同样的概念也适用于函数作用域(局部作用域)。函数内部的所有变量声明都将被提升到函数的顶部，不管它们是在函数中的什么地方声明的。

```
function greet(){
  if(true){
    var msg = 'Hello World';
  }
  console.log(msg);
}

greet();
console.log(msg); 
```

Enter fullscreen mode Exit fullscreen mode

上面的程序是由 JavaScript 解释器翻译的，就好像 var `msg`被放在函数的顶部，尽管它是在`if`块中声明的。

```
function greet(){
  var msg;
  if(true){
    msg = 'Hello World';
  }
  console.log(msg);
}

greet();
console.log(msg); 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们得到输出–`Hello World`

### 遮蔽

如果我们用相同的名字声明一个局部变量和一个全局变量，那么当局部变量在函数中被引用时，它将优先。它叫做*遮蔽*，内部变量遮蔽外部变量。Javascript 解释器在最内部的范围搜索变量，直到找到第一个匹配项。

```
var msg = 'Hello ';
var name = 'John';
function greet(){
  var msg = 'Julia';
  if(true){
    console.log(msg + name); //Hello Julia
  }
}

greet(); 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 解释器执行`if`块时，它会在局部范围内寻找变量`msg`。正如在那里发现的那样，它使用局部作用域变量，即使在外部作用域中也声明了相同的变量。变量`name`在内部作用域中没有找到，所以 JS 解释器试图在外部作用域中找到它，结果找到了。

> 作用域和提升对函数的作用与对变量的作用相同。

### 与`var`的问题

不，我们已经理解了与`var`相关的不同概念，让我们讨论一下 var 的问题以及为什么应该避免使用它。

让我们举个例子:

```
var greetings = 'Hello John';

function greet() {
  console.log(greetings); // undefined
  if (true) {
    var greetings = 'Hello Julia';
    console.log(greetings); //Hello Julia
  }
  console.log(greetings); //Hello Julia
}

greet(); 
```

Enter fullscreen mode Exit fullscreen mode

如果您来自另一种编程语言背景，如 Java、Python、C#等，您一定会想“见鬼去吧”。你不认为问候是一个全局变量吗？第一个输出应该是`Hello John`？第三个输出应该是`Hello John`,因为虽然在`if`块中我们声明了同一个变量`greetings`,但是在`if`块之外它应该是不可见的？

不正确！

我们刚刚学习了 JavaScript 中的**作用域**、**提升**和**隐藏**。我们知道 JavaScript 中没有**块范围**的概念(直到 ES5)。让我们将这些知识应用到上面的例子中。

当 JavaScript 解释器在本地`if`块中遇到`greetings`的变量声明时，它*在函数的顶部提升*变量声明。但是只挂声明，不挂初始化。该变量具有 JavaScript 解释器在声明时分配的默认值`undefined`。当在第一个 print 语句中引用`greetings`时，由于*shadowing*原因，JavaScript 解释器使用了`greetings`变量的内部声明，而不是全局声明。但是由于`greetings`的内部声明还没有初始化，它打印了它的默认值`undefined`。

同样的概念*提升*和*遮蔽*在第三个输出中得到了应用。解释器接受了`greetings`的函数作用域声明。唯一不同的是这次`greetings`已经在`if`块中初始化了。

> 正如我们所见，用`var`声明的变量令人困惑，如果不小心编写，很容易在程序中引入错误。这就是为什么你不应该在你的程序中使用`var`来声明变量。总是使用`let`或`const`来声明变量。

## `let`ES6 中的关键字

ES6 规范用 JavaScript 解决了这些问题，并引入了**块范围* *和一个新的关键字`let`。

让我们举一个与上一个例子相同的例子，只是有所改变——用关键字`let`替换`var`:

```
let greetings = 'Hello John';

function greet() {
  console.log(greetings); //Hello John
  if (true) {
    let greetings = 'Hello Julia';
    console.log(greetings); //Hello Julia
  }
  console.log(greetings); //Hello John
}

greet(); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们看到的，使用关键字`let`可以更好地预测输出。第一次在 print 语句中引用`greetings`时，它采用了全局值，因为它还没有在本地声明。JavaScript 解释器在`if`块的局部范围内声明了`greeting`的第二个实例。它将本地值`Hello Julia`保留在`if`块中。但是在声明变量的`if`块之外，相同的`greetings`变量对于解释器是不可见的。因此，它在第三个打印声明中采用了全局声明。

正如我们所见，`let`关键字的行为与大多数其他编程语言相似，并且具有*块范围*。这就是为什么我们应该“主要”使用`let`关键字来声明 JavaScript 中的变量，而不是使用`var`。

## `const`ES6 中的关键字

我希望你已经注意到之前的声明——我们应该“**主要是**使用`let`关键字。我们之所以使用它，主要是因为当我们有永远不会改变其值的变量时，我们应该用`const`关键字来声明它们。这是一个很好的安全特性，因为如果程序的任何部分试图改变变量的值，JavaScript 解释器就会引发异常。

让我们看看实际情况:

```
function greet() {
  const greetings = 'Hello World';
  if (true) {
    greetings = 'Hello Julia';
    console.log(greetings);
  }
}

greet(); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行这段代码，JavaScript 解释器将会在我们试图改变变量`greetings`的值时抛出一个异常。

```
Uncaught TypeError: Assignment to constant variable.
    at greet (<anonymous>:4:15)
    at <anonymous>:9:1 
```

Enter fullscreen mode Exit fullscreen mode

`let`和`const`没有其他区别

## 结论

我们希望本教程能让你很好地理解 JavaScript 中声明变量的三种方式。我们讨论过,`var`没有块作用域，它容易出错，我们不应该使用它。我们应该用 ES6 的`let`或者`const`来代替。我们还了解到，当我们想要限制一个变量时，我们应该使用`const`,这样它就不会改变它的值并保持不变。