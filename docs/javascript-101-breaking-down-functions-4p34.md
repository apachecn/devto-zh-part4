# JavaScript 101:分解函数

> 原文：<https://dev.to/karaluton/javascript-101-breaking-down-functions-4p34>

函数是我们在 JavaScript 中所做工作的核心，所以理解它们的作用和工作方式是非常重要的。但是函数到底是什么呢？JavaScript 函数是执行特定任务的代码块，当某个东西调用它时就会执行。把它想象成一个食谱，给你需要做的东西的成分(参数)和方向(功能体)。

## 定义一个函数

这里有一个函数被分解成你所需要的:名字、参数和函数体。

```
function name(parameter) {
 // function body
} 
```

Enter fullscreen mode Exit fullscreen mode

定义函数的第一步是给函数命名。您的函数名可以包含字母、数字、下划线和美元符号。例如，您的函数可以命名为`helloWorld`、`helloWorld1`、`hello_world`或`$helloWorld`。

```
function helloWorld() {
 // 
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要定义函数的参数。函数参数放在函数的括号内，用逗号分隔。

```
function helloWorld(name) {
 // code to be executed
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个多参数函数的例子。

```
function helloWorld(name, age) {
 // code to be executed
} 
```

Enter fullscreen mode Exit fullscreen mode

> 快速提示！您可能听说过术语“参数”和“自变量”也是以同样的方式使用的。它们非常相似，但不应该用来描述同一个事物。*参数*在定义函数时使用(括号内的内容)，而*参数*是函数执行时从每个参数接收的值。
> 
> 所以如果你定义一个变量比如`const param1 = 'Hello World';`然后定义你的函数`function helloWorld(param1) {}`。括号内的是你的参数，而你定义为“Hello World”的`param1`变量是你的自变量。

最后，要执行的代码将放在花括号内。所有的一切看起来都是这样的:

```
function helloWorld(name) {
 console.log('Hello ' + name);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 调用函数

太好了！我们已经定义了我们的函数，但是我们如何让它运行呢？您可以通过引用后跟括号的函数名来调用(或运行)函数。让我们调用之前定义的函数。

```
helloWorld('Kara');

// Hello Kara 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我将“Kara”作为参数传入，并调用我们的函数。然后`Hello Kara`被记录到控制台！

现在你知道了！您现在知道了如何定义和调用您的函数。请记住，所有函数都将**总是**返回值。如果没有函数体，那么你的函数将返回未定义。

```
function helloWorld() {}; // define your function

helloWorld(); // invoke your function

// undefined 
```

Enter fullscreen mode Exit fullscreen mode

* * *

一定要在 Twitter 上关注我的许多关于科技的帖子，如果我是诚实的，也有许多关于狗的帖子。