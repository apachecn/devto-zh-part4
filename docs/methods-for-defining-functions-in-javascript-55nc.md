# JavaScript 中定义函数的方法

> 原文：<https://dev.to/bnevilleoneill/methods-for-defining-functions-in-javascript-55nc>

**作者:[亚什·阿格拉瓦尔](https://blog.logrocket.com/author/yashagrawal/)T3】✏️**

通常，函数是一系列指令或“子程序”,可由该函数外部(或内部)的代码调用。本质上，函数“封装”了一个特定的任务。

函数是 JavaScript 的基本构建模块之一，真正理解函数有助于解决 JavaScript 的一些奇怪问题(T3)。

## JavaScript 中的函数

需要注意的是，JavaScript 中的函数是一级对象。这基本上意味着 JavaScript 中的函数可以像任何其他 JavaScript 对象一样对待，可以作为其他变量引用或作为参数传递给函数。

函数甚至可以有属性和其他方法，就像任何其他 JavaScript 对象一样。函数和其他对象的关键区别在于函数可以被调用。

JavaScript 中的每个函数都是一个`Function`对象。你可以进入控制台试试这个:

```
function typeCheck() {};
typeCheck instanceof Function // Logs True 
```

Enter fullscreen mode Exit fullscreen mode

`Function`对象有一些特定的方法和属性，如`apply`、`call`、`bind`、`isGenerator`等。其他对象不可用的。

JavaScript 中有几种不同的函数定义方式，定义方式会影响函数的行为。让我们逐一探索每一种方法。

### 功能声明

这可能是定义函数最熟悉的方式。函数声明由一个名字组成，名字前面是强制的关键字`function`，后面是一个可选的参数列表，在一对必需的括号`()`内。

```
function sum(param1, param2) {
  return param1 + param2;
} 
```

Enter fullscreen mode Exit fullscreen mode

关于这种形式的函数定义，有两点需要注意:

*   保存函数对象的变量是在当前作用域中创建的，其标识符与所提供的函数名相同——在我们的示例中为`sum`。
*   变量**被提升**到当前作用域的顶部。你可以[在这里](https://www.yagrawal.com/hoisting-in-javascript)了解更多。

为了更好地理解吊装，我们来看一个例子:

```
console.log(notYetDeclared()); // Logs 'Here!'

function notYetDeclared() {
  return 'Here';
} 
```

Enter fullscreen mode Exit fullscreen mode

我们能够在定义函数`notYetDeclared`之前调用它。

### 函数表达式

函数表达式在语法上与函数声明非常相似。主要区别在于函数表达式不需要函数名。

```
let sum = function(param1, param2) {
  return param1 + param2;
}; 
```

Enter fullscreen mode Exit fullscreen mode

函数表达式是另一个语句的一部分。在上面的例子中，函数表达式是`sum`变量赋值的一部分。

与函数声明不同，函数表达式是*而不是*提升的。

```
console.log(notYetDeclared); // Logs 'undefined'

let notYetDeclared = function() {
  return 'Here';
} 
```

Enter fullscreen mode Exit fullscreen mode

函数表达式的一个有趣的用例是它们创建函数或直接调用函数表达式的能力。在某些情况下，我们可能希望定义一个函数，然后在定义之后立即调用它，但以后不会了。

当然，这可以通过函数声明来实现，但是为了使它更具可读性，并确保我们的程序不会意外地访问它，我们使用了一个 IIFE。考虑这个例子:

```
function callImmediately(foo) {
  console.log(foo);
}

callImmediately('foo'); // Logs 'foo' 
```

Enter fullscreen mode Exit fullscreen mode

我们创建一个名为`callImmediately`的函数，它接受一个参数并记录下来，然后我们立即调用它。这样做可以得到相同的结果:

```
(function(foo) {
  console.log(foo);
})('foo'); // Logs 'foo' 
```

Enter fullscreen mode Exit fullscreen mode

关键的区别在于，在第一种情况下，函数声明污染了全局名称空间，而命名函数`callImmediately`在被需要后很长时间都悬而未决。生命是匿名的，因此不能在未来被调用。

### 箭头功能

[箭头函数](https://dev.to/bnevilleoneill/anomalies-in-javascript-arrow-functions-2afh)是 ES6 的一个附加功能，是函数表达式的一个语法紧凑的替代。使用一对包含参数列表的括号来定义箭头函数，后跟一个粗箭头`=>`，然后是带花括号的函数语句`{}`。

```
let sum = (param1, param2) => {
  return param1 + param2;
}; 
```

Enter fullscreen mode Exit fullscreen mode

因为 arrow 函数背后的主要动机之一是语法紧凑性，如果 arrow 函数中唯一的语句是`return`，我们可以去掉花括号和`return`关键字，比如:

```
let sum = (param1, param2) => param1 + param2; 
```

Enter fullscreen mode Exit fullscreen mode

此外，如果我们只有一个参数传递给 arrow 函数，那么就可以去掉 paren:

```
let double = param1 => param1 * 2; 
```

Enter fullscreen mode Exit fullscreen mode

在这种形式的函数定义中，需要注意的一些重要事项是:

*   arrow 函数没有自己的`this`，它使用封闭词法范围的`this`值。你可以在这里阅读更多关于`this`。

```
let foo = {
    id: 10,
    logIdArrow: () => { console.log(this.id) },
    logIdExpression: function() {
      console.log(this.id);
    }
  }

  foo.logIdArrow(); // Logs 'undefined'
  foo.logIdExpression(); // Logs '10' 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们有一个箭头函数和一个使用`this`记录`foo.id`的函数表达式。

*   箭头函数没有`prototype`属性。

```
let foo = () => {};
console.log(foo.prototype); // Logs 'undefined' 
```

Enter fullscreen mode Exit fullscreen mode

*   箭头函数中没有`arguments`对象。你可以在这里阅读更多关于`arguments`物体[的信息](https://www.yagrawal.com/parameters-&-arguments-in-javascript)。

### `Function`构造函数

如前所述，JavaScript 中的每个函数都是一个`Function`对象，所以要定义一个函数，我们也可以直接调用`Function`对象的构造函数。

```
let sum = new Function('param1', 'param2', 'return param1 + param2'); 
```

Enter fullscreen mode Exit fullscreen mode

参数作为逗号分隔的字符串列表`'param1', 'param2', ..., 'paramN'`传递，最后一个参数是作为字符串传递的函数体。

就性能而言，这种定义函数的方式不如函数声明或函数表达式有效。使用`Function`构造函数定义的函数在每次调用构造函数时都会被解析，因为每次都需要解析函数体字符串，这与其他函数不同，其他函数是用代码的其余部分解析的。

以这种方式定义函数的一个用例是访问 Node 中的`global`对象或浏览器中的`window`对象。这些函数总是在全局范围内创建，并且不能访问当前范围。

### 发电机功能

发电机是 ES6 的附加物。生成器是一种特殊类型的函数，与传统函数不同，生成器在每个请求的基础上生成多个值，同时在这些请求之间暂停它们的执行。

```
function* idMaker() {
  let index = 0;
  while(true)
    yield index++;
}

let gen = idMaker();

console.log(gen.next().value); // Logs 0
console.log(gen.next().value); // Logs 1
console.log(gen.next().value); // Logs 2 
```

Enter fullscreen mode Exit fullscreen mode

`function*`和`yield`关键字是生成器独有的。生成器是通过在函数关键字的末尾添加一个`*`来定义的。这使我们能够在生成器主体中使用`yield`关键字来根据请求生成值。

你可以在这里阅读更多细节[。](https://www.yagrawal.com/generators-in-javascript)

## 结论

选择使用哪种定义类型取决于具体情况和您想要达到的目标。需要记住的几个一般要点:

*   如果您想利用函数提升，请使用函数声明——例如，为了清楚起见，您想将函数实现细节移到底部，而只将抽象流放在顶部。
*   箭头函数非常适合简短的回调函数，更重要的是，当期望的`this`是封闭函数时。
*   避免使用`Function`构造函数来定义函数。如果讨厌的语法还不足以让你远离的话，那么它会非常慢，因为每次调用函数时都会被解析。

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

在 JavaScript 中定义函数的方法[这篇文章最早出现在](https://blog.logrocket.com/defining-functions-in-javascript/)[博客](https://blog.logrocket.com)上。