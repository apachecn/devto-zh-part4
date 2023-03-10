# ES6 |全面的 ES2015 学习指南(ES6)

> 原文：<https://dev.to/dipakkr/es6-a-comprehensive-guide-to-learn-es2015-es6-2ao1>

ES6 是一种 JavaScript 编码标准，也称为 ECMASCRIPT2015，于 2015 年发布。它有许多 javascript 开发人员应该知道并在代码中实现的优秀特性。

今天，我们将了解 ES6 的几乎所有功能。为了更好地理解，我还给出了每个特性的例子。

我建议你先学习一个部分，然后再去学习另一个部分。

那么，让我们开始吧！！！

我的 [Github](https://github.com/dipakkr) 账号:【https://github.com/dipakkr/ES6-Guide】T2 也有这篇文章

## 目录

1.  [Var，let 和 Const](#1-var-let-and-const)
2.  [模板文字](#2-template-literals)
3.  [默认参数](#3-default-arguments)
4.  [箭头功能](#4-arrow-functions)
5.  [数组和对象析构](#5-array-and-object-destructuring)
6.  [映射、缩小和过滤](#6-map-reduce-and-filter)
7.  [迭代和循环](#7-iterables-and-looping)
8.  [休息和伸展操作者](#8-rest-and-spread-operator)
9.  [对象文字量](#9-object-literals)
10.  [ES6 中的类](#10-classes-in-es6)
11.  [承诺](#11-promises)

* * *

在我们继续之前，让我自我介绍一下。

我是 Deepak Kumar，一个全栈 JavaScript 开发人员，自由职业者，一个崭露头角的企业家。我最近创办了一家初创公司`FrontBench`，旨在帮助学生获得平等的机会、合适的导师和指导。我喜欢构建和扩展对社区有真正影响的产品。

你可以在[LinkedIn](http://bit.ly/2MQX5pK)|[insta gram](http://bit.ly/2lU6pBm)|[Twitter](http://bit.ly/2mcRaUk)|[Github](https://github.com/dipakkr)上和我联系

* * *

让我们学习 ES6。

### 1。Var，let 和 const

**1.1 有一个**

*   Var 关键字以前用于在 javascript 中声明变量。
*   用 var 声明的变量也可以重新初始化和重新声明。
*   在`let`和`const`发布后**不建议**使用`var`。

```
 var a = 10;

    for(var i=0;i<5;i++){
            var a = 20;
            console.log(a); //Returns 20
    }

    console.log(a); // Returns 20 
```

Enter fullscreen mode Exit fullscreen mode

**1.2 让**

*   当您必须在代码的后面更改变量值时，使用“let”。
*   它具有块范围。
*   它可以重新初始化，但不能重新声明。

```
 let a = 10;

    // re-initialization
    a = 30; // Updating a value to 30.

    //re-declartion
    let a = 20; // Throws Error

    // Block 1
    {
         let c = 10;
         console.log(c); // c=10
    }

    console.log(c); // Throws Error, c not defined. 
```

Enter fullscreen mode Exit fullscreen mode

**1.3 常数**

*   Const 用于定义一个在整个代码中不能改变的常量变量。
*   它具有块范围。
*   你既不能被重新启动，也不能被重新声明。

```
 const a = 10;

    // re-initialization
    a = 30; // Throws Error, CONST variable can't be changed

    //re-declartion
    const a = 20; // Throws Error

    // Block 1
    {
         const c = 10;
         console.log(c); // c=10
    }

    console.log(c); // Throws Error, c not defined. 
```

Enter fullscreen mode Exit fullscreen mode

### 2。模板文字

* * *

模板文字是允许嵌入表达式的字符串文字。您可以使用多行字符串和字符串插值功能。在 ES2015 规范的早期版本中，它们被称为“模板字符串”。

模板文字基本上是 javascript 中字符串的格式。在 ES5 中，格式化字符串是一项单调乏味的任务，因为它涉及非常手工的格式化语法。

让我们看一个在 ES5 中如何格式化字符串的例子。

```
 # TEMPLATE STRING (WITHOUT ES6)

    function greet(name){
        const greeting = 'Hello,' + ' ' + name + ' ' + Welcome to JavaScript Course;
        return greeting;
    }

    greet('Deepak');

    // Hello, Deepak Welcome to JavaScript Course. 
```

Enter fullscreen mode Exit fullscreen mode

```
 # TEMPLATE STRING (WITH ES6)

    function greet(name){
        const greeting = `Hello, ${name} Welcome to JavaScript Course`;
        return greeting;
    }

    greet('Deepak');

    // Hello, Deepak Welcome to JavaScript Course. 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以看到使用 ES6 新语法的格式字符串是多么容易。

**重述**

*   模板字符串用反斜线(``)括起来，而不是用单引号或双引号括起来。
*   模板文字可以包含占位符。这些由美元符号和花括号(\${expression})表示。占位符中的表达式和反勾号(``)之间的文本被传递给一个函数。

### 3。默认参数

* * *

默认自变量或默认参数是 ES6 中的新特性。如果**没有值**或**未定义**被传递，它允许您为您的函数参数/自变量设置默认值。

**用 ES5**
处理默认参数

```
 function add(a, b){
            return a + b;
    }

    add() // NaN

    // Handling Default Argument without ES6.

    function add(a, b){
        const a = (typeof(a) !== 'undefined') ? a : 5;
        const b = (typeof(b) !== 'undefined') ? b : 10;
      return a+b;
    }

    add() // Returns 15 
```

Enter fullscreen mode Exit fullscreen mode

当没有参数被传递时，你可以看到我们必须通过设置默认值 a & b 来显式地处理错误。这看起来不像是处理默认参数的好方法。

**用 ES6**
处理默认参数

```
 function add(a=5, b=10){
        return a+b;
    }

    add(); // a=5, b=10, sum = 15;

    add(2, 3); // a=2, b=3, sum = 5;

    add(4); // a=4, b=10, sum=14 ; 
```

Enter fullscreen mode Exit fullscreen mode

只有在没有传递参数时，才会使用 A 和 B 的默认值。

### 4。箭头功能

* * *

箭头函数在语法上是正则函数表达式的紧凑替代形式，它本身不绑定到`this`、`super`、

```
**Using Regular Function Express (ES5)**

    // Example 1
    function add(a, b){
        return a+b;
    }

    add(5, 10);

    // Example 2

    const x = [1, 2, 3, 4, 5];

    const square = x.map(function(x){
        return x*x;
    });

    console.log(sqaure); 
```

Enter fullscreen mode Exit fullscreen mode

**使用箭头功能(ES6)**

```
 // Example 1
    const add = (a, b) => {
            return a+b;
    }

    add(5, 10)

    //Example 2

    const x = [1, 2, 3, 4, 5];

    const square = x.map(num => num*num);
    console.log(sqaure); 
```

Enter fullscreen mode Exit fullscreen mode

### 5。数组和对象析构

* * *

析构是 ES6 中引入的新特性，用于解包数组中的值或对象中的属性。它有助于提高代码的可读性和性能。

**ES5**中的解构

```
 // Example 1 - Object Destructuring

    var user = {
        name : 'Deepak',
      username : 'dipakkr',
      password : 12345
    }

    const name = user.name; // Deepak
    const username = user.username; // dipakkr
    const password = user.password // 12345

    //Example 2 - Array Destructing

    *c*onst fruits = ["apple", "mango", "banana", "grapes"];

    const fruit1 = fruits[0];
    const fruit2 = fruits[1];
    const fruit3 = fruits[2]; 
```

Enter fullscreen mode Exit fullscreen mode

**ES6**中的解构

```
 // Example 1 - Object Destructuring

    var user = {
        name : 'Deepak',
      username : 'dipakkr',
      password : 12345
    }

    const {name, username, password} = user;
    console.log(name);
    console.log(username);
    console.log(password);

    //Example 2 - Array Destructing

    const fruits = ["apple", "mango", "banana", "grapes"];

    const [fruit1, fruit2, fruit3] = fruits;

    console.log(fruit1); // apple
    console.log(fruit2); // mango
    console.log(fruit3); // banana 
```

Enter fullscreen mode Exit fullscreen mode

### 6。映射、减少和过滤

* * *

Map、Reduce 和 Filter 是 ES6 中引入的数组方法。这三种方法的共同点是，当这些方法应用于一个数组时，它会根据给定的参数返回一个新的数组。

**地图方法**

让我们通过一个简单的例子来理解 Map 方法。假设您有一个包含多个用户对象的用户数组。但是，您只需要每个用户的用户名。

你会怎么做？这里有一种方法。

```
 const users = [
      { name: 'Deepak', username: 'dipakkr', password: '123456'},
      { name: 'Rohan', username: 'rohan12', password: '198243' },
      { name: 'Sam', username: 'sam124', password: '123876' },
    ];

    var usernames = [];

    users.forEach(function(user) {
      usernames.push(user.username);
    });

    console.log(usernames); // [ 'dipakkr', 'rohan12', 'sam124', 'ro123' ] 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们用`map()`的方法来解决这个问题。

```
 const users = [
      { name: 'Deepak', username: 'dipakkr', password: '123456'},
      { name: 'Rohan', username: 'rohan12', password: '198243' },
      { name: 'Sam', username: 'sam124', password: '123876' },
    ];

    const usernames = users.map(user => user.username);

    console.log(usernames); // [ 'dipakkr', 'rohan12', 'sam124', 'ro123' ] 
```

Enter fullscreen mode Exit fullscreen mode

**过滤方法**

Filter 方法接受一个应用于每个数组元素的函数参数，然后满足参数条件的元素在新数组中返回。

```
 const number = [5, 1, 4, 10, 15, 20, 12];

    const result = number.filter(num => num>10);

    console.log(result); // [15, 20, 12]; 
```

Enter fullscreen mode Exit fullscreen mode

### 7。迭代和循环

* * *

以下是 JavaScript 中的交互变量列表。

| 可迭代的 | 描述 |
| --- | --- |
| 排列 | 通过迭代数组来访问每个元素。 |
| 地图 | 迭代键值对 |
| 用线串 | 通过迭代字符串来访问每个字符 |
| 设置 | 迭代集合元素 |
| 争论 | 通过迭代参数来访问每个参数 |

* * *

`for...of`是 ES6 中引入的一个新特性，可以更容易地访问 interables 元素。 ***为...of*** 语句简单地创建了一个遍历可迭代对象的循环。

**无`for...of`循环**

```
const array = [5, 10, 15, 20, 25, 30, 35];

for(var value in array){
    console.log(array[value]);
}

// To access the element of the array, We are using array[postion] notation. 
```

Enter fullscreen mode Exit fullscreen mode

**用`for...of`循环**

```
const array = [5, 10, 15, 20, 25, 30, 35];

for(var value of a){
    console.log(value);
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们可以看到，我们能够用 for 直接访问可交互的元素...的方法。

### 8。休息和传播算子

* * *

Spread 和 Rest 操作符由三个点`...`表示。这三个点有两种用法，一种是作为`Spread Operator`，另一种是作为`Rest Parameter`

⇒ **Rest Parameter**

*   它将所有剩余的元素收集到一个数组中。
*   Rest 参数可以将任意数量的参数收集到一个数组中。
*   Rest 参数必须是最后一个参数。

**不使用休息参数**

```
 // Write a Function to print sum of arguments.

    function add() {
      var sum = 0;
      for (var i = 0; i < arguments.length; i++) {
        sum = sum + arguments[i];
      }
      return sum;
    }

    console.log(add(1, 2, 3, 4, 5)); // 15

    console.log(add(1, 3, 4)); // 8 
```

Enter fullscreen mode Exit fullscreen mode

**使用 Rest 运算符的例子**

```
 function add(...args) {
      let sum = 0;
      for (let i of args) {
        sum += i;
      }
      return sum;
    }

    console.log(add(3, 4, 5, 10, 20)); // 42

    console.log(add(1, 3, 4)); // 8 
```

Enter fullscreen mode Exit fullscreen mode

传播算子

*   它允许像`arrays / objects /strings`这样的可重复项扩展成单个参数/元素。
*   扩展运算符与其余参数相反。在 Rest Parameter 中，我们将参数列表收集到一个数组中，而使用 spread operator，我们可以解开数组元素。

我们来看一个例子来理解`spread`

```
 ## EXAMPLE - 1

    const cars = ['BMW', 'Honda', 'Audi'];
    const moreCars = ['Maruti', 'Swift', ...cars];

    console.log(moreCars);  // ['Maruti', 'Swift', 'BMW', 'Honda', 'Audi'];

    ## EXAMPLE - 2 //Copying one array to other

    const array1 = [1, 2, 3];
    const copiedArray = ...array1;

    console.log(copiedArray); // [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

### 9。**对象字面量**

* * *

对象文字用于在 javascript 中创建对象。ES2015 (ES6)版本中对象文字的增强使其更加强大。

*   可以通过直接使用变量名来初始化对象。参见下面的示例 1。
*   ES5 中对象的方法需要`function`语句。这在 ES6 中不再需要，可以直接返回语句。参见下面的示例 2。
*   ES6 中的对象文字键可以是动态的。任何 Express 都可以用来创建密钥。

让我们看看这个例子，看看对象文字的工作原理。

**不带 ES6 的对象文字(支持 ES5)**

```
 # Example 1

    var username = 'dipakkr'
    var name = 'Deepak Kumar'
    var country = 'India'
    var password = '123456'

    var user = {
        username : username,
      name : name,
        country : country,
        password : password
    }

    # Example 2

    var calculate = {
      sqaure :  function(a) { return a*a; },
      sum : function(a, b) { return a + b; }
    };

    console.log(calculate.square(5));  // 25
    console.log(calculate.sum(4,5));   //  9 
```

Enter fullscreen mode Exit fullscreen mode

**带 ES6 的对象文字量**

```
 # Example 1

    const username = 'dipakkr'
    const name = 'Deepak Kumar'
    const country = 'India'
    const password = '123456'

    const user = {
        username,
        name,
        country,
        password,
    };

    # Example 2

    const calculate = {
        square(a) return a*a,
      sum(a, b) return a+b
    }

    console.log(calculate.square(5));   // 25
    console.log(calculate.sum(5,7));    // 12 
```

Enter fullscreen mode Exit fullscreen mode

### 10。**ES6 中的班级**

* * *

ECMAScript 2015 中引入的 JavaScript。类支持基于原型的继承、构造函数、超级调用、实例和静态方法

JavaScript 中有两种定义类的方法。

1.  类别声明
2.  类别表达式

**类声明**

为了使用-declaration 方法定义类，您需要使用`class`关键字，后跟 className。类名必须以大写字母开头。

```
 class Rectangle {
      constructor(height, width) {
        this.height = height;
        this.width = width;
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

**类表达式**

类表达式是定义类的另一种方式。类表达式可以是命名的，也可以是未命名的。指定给命名类表达式的名称对于类的主体来说是局部的。

```
 let Rectangle = class {
      constructor(height, width) {
        this.height = height;
        this.width = width;
      }
    };

    console.log(Rectangle.name); 
```

Enter fullscreen mode Exit fullscreen mode

Mozilla Developer 对 javascript 类有很好的解释。更多阅读 [**此处**](https://mzl.la/2mcPSZu)

### 11。承诺

* * *

为了支持异步编程，JavaScript 使用了回调。然而，回调实现有一个主要问题，称为`Callback hell.`承诺来拯救解决回调地狱的问题。

**Promises** 是一种模式，通过使代码看起来同步并避免与回调相关的问题，极大地简化了异步编程。

承诺有三种状态。

*   **待定**:初始状态，既未履行也未拒绝。
*   **完成**:表示操作成功完成。
*   **拒绝**:表示操作失败。

[![Promises](img/df9258fb0ccd0a835c54f9f6dd0d5810.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E9fM8gVi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./assets/promises.png) 
*图片来源:MDN*

```
let promise = new Promise(function(resolve, reject) {
      setTimeout(() => resolve('Success ! '), 2000);
    });

    promise
      .then(function(result) {
        console.log(result);
      })
      .catch(function(error) {
        console.log(error);
      }); 
```

Enter fullscreen mode Exit fullscreen mode

```
RESULT

Success! 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于`Promises`的信息，请点击[链接](http://bit.ly/2lQy1HP)

* * *

**[关于作者](#about-the-author)**

嗨，我是 Deepak Kumar，全栈 JavaScript 开发人员，自由职业者。我喜欢构建和扩展对社区有真正影响的产品。

[![Twitter Follow](img/cedc1cc89b8d6c891c90fd5f70e3a2cd.png)](https://twitter.com/diipakkr)

我们连线吧！-|[LinkedIn](http://bit.ly/2MQX5pK)|[insta gram](http://instagram.com/dipakkr.co)|[Twitter](https://twitter.com/HQdeepak)

订阅我的电子邮件简讯，随时了解最新消息！

* * *