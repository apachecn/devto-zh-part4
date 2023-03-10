# 何时使用不同的变量和函数声明，最佳实践是什么？

> 原文：<https://dev.to/antjanus/why-use-arrow-vs-named-functions-why-use-let-and-const-instead-of-var-5cbi>

如果您是新手或经验丰富的开发人员，您可能会查看代码和代码风格的建议，并想知道，为什么这些实践被认为是最佳实践？

嗯，我不想打断你，但是大多数风格偏好和最佳实践更多的是偏好和主观上非常“最佳”。事实上，在 web 开发和 JavaScript 中有各种各样的“阵营”,它们偶尔会在 Twitter 上就像分号的用法这样平凡的事情发生冲突。

因此，在本文中，我想讨论函数和变量，讨论它们的主观“最佳实践”以及不使用它们的原因。

## 箭头函数与命名函数

声明函数有多种方式。arrow 函数允许您跳过单参数函数中的括号，并且命名函数可以声明为独立函数或赋给变量。这里有一个简单的例子:

```
function sum(leftNum, rightNum) {
  return leftNum + rightNum;
}

const sum = (leftNum, rightNum) => { 
  return leftNum + rightNum;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们来看看这两个函数声明的特性差异。

### 吊装

第一个大的是吊装。如果你在一个作用域块之外声明一个命名函数(比如一个`do`或者另一个函数或者一个类)，它将在运行时自动地随处可用！这允许你用一个清晰的顺序来组织你的 JavaScript 文件，而不用考虑一个函数是否可用。比如:

```
bootstrapApplication(); // this runs without an issue

function bootstrapApplication() {
  // logic here
} 
```

Enter fullscreen mode Exit fullscreen mode

但是这不适用于箭头函数:

```
bootstrapApplication();

const bootstrapApplication = () => {
  // logic goes here
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，常量在声明之前是不可用的。

如果你看看[我写的关于我如何构建 javascript 文件](https://dev.to/antjanus/how-i-structure-my-javascript-file-2clc)的帖子，你会看到我充分利用了它，还有更多例子。

### 令人畏惧的`this`

`this`指的是你当前正在编写代码的对象。无论它是一个实际的 JavaScript 对象还是一个类，或者一个函数表达式，`this`将允许你访问原型，它将允许你访问其他内部变量。

命名函数和函数表达式都在其作用域内创建自己的`this`,这可能会令人困惑，因为您并不总是希望这样做。

```
// using function expression
function counterApp(arr) {
  this.count = 0;

  arr.forEach(function (item) {
    this.count++; // which `this` is this?
  }); 

  return this.count;
}

// using named functions

function counterApp(arr) {
  this.count = 0;

  function namedFunction(item) {
    this.count++; // is `this` available
  }

  arr.forEach(namedFunction); 

  return this.count;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下，`this`实际上是指它所调用的函数内部的`this`。在第一个例子中，`this.count`是未定义的，第二个例子也是如此。

Arrow function 的流行就是源于这个问题。回到过去，在这两个例子中，你可以将`this`赋给变量:`var self = this`并运行`self.count++`。事实上，这就是 Babel 和其他流行工具 transpile arrow 的功能。

下面是使用箭头功能可以做的事情:

```
// using arrow function
function counterApp(arr) {
  this.count = 0;

  arr.forEach(item => {
    this.count++; // which `this` is this?
  }); 

  return this.count;
}

// using arrow function assigned to a variable
function counterApp(arr) {
  this.count = 0;

  const namedFunction = (item) => {
    this.count++;
  };

  arr.forEach(namedFunction); 

  return this.count;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这两个例子中，`this.count`是您所期望，且引用了`counterApp`的内部`count`变量。

### 速记

与命名函数相比，arrow 函数的一个优势是它有非常简洁的快捷键:

```
// single argument functions can drop the parentheses
const doubleNumber = num => { 
  return num * 2;
};

// any arrow function can drop the curly braces and return 
// should the `return` be the only code in the function body
const multiplyNums = (num1, num2) => num1 * num2;

// this gets tricky if you're returning an object
// you can wrap the object in parentheses like so:
const initializeUser = userName => ({ name: userName, newUser: true }); 
```

Enter fullscreen mode Exit fullscreen mode

### 为什么要用这个或者那个？

我还没有遇到一个开发者会告诉你在所有的情况下使用其中的一种。相反，有一些*最佳实践*你可以遵循，我会分享这些，只是要记住这是超级主观的，大多数是个人喜好。

#### 何时使用箭头功能

```
// closures/callbacks
const userIds = users.map(user => user.id);

// when you need to access `this`
function application() {
  this.count = 0;

  const button = document.querySelector('.increment-button');

  button.addEventListener('click', () => this.count++);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 何时使用命名函数

```
// when hoisting is important
function applicationBootstrap(users) {
  const userIds = users.map(getProperty('id'));
}

// gets hoisted and is available for `applicationBootstrap`
function getProperty(prop) {
  // oooh, mixing things up!
  return item => item[prop];
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 其他地方呢？

全靠你了！说真的。模块导出中的箭头函数还是命名函数？你的电话。

## 设 vs 常数 vs Var

哦不，哦不，哦不。我不碰这个...

好吧，我是。尤其是因为我已经用了很多次`const`。

**注意**请假设我在`strict`模式下运行一切。

### 范围

很久以前，我们从`var`开始。`var`正如它所说的那样:它声明了一个变量！那个变量存在于一个`scope`中。一个`scope`通常是一个函数(任何类型的函数！)、模块或对象。

```
var hello = 'hi';

function welcome() {
  var worldName = 'world';
  console.log(hello, worldName); // accessible
}

console.log(worldName); // not accessible and undefined 
```

Enter fullscreen mode Exit fullscreen mode

酷，记住这一点。您可以在 for 循环和 while 循环中声明一个变量，该变量将存在于它们之外:

```
for (var i = 0; i < 10; i++) {
  var finalCount = i;
}

console.log(finalCount); // accessible
console.log(i); // accessible as well! 
```

Enter fullscreen mode Exit fullscreen mode

`let`和`const`都遵循对象和函数的作用域规则，但不遵循 for 和 while 循环的规则。所以如果你写一个相同的 for 循环:

```
let initialCount = 0;

for (let i = 0; i < 10; i++) {
  let  finalCount = i;
}

console.log(initialCount); // only one accessible
console.log(finalCount); // inaccessible
console.log(i); // inaccessible as well! 
```

Enter fullscreen mode Exit fullscreen mode

### 变量赋值

`var`和`let`都让你在初始化后重新分配一个变量:

```
var initialized = false;

let userRole = 'read only';

if (userId === 1) {
  userRole = 'admin';
}

initialized = true; 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中,`var`和`let`都是可互换的；然而，`const`应该是一个`constant`，意味着一个变量一旦被声明就不能被重新赋值:

```
fucntion preciseNumber(num) {
  const precision = 8;

  if (num > 10) {
    precision = 4; // this will throw an error
  }

  return num.toFixed(precision);
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，注意我说的`reassignment`，*不是*突变。有什么区别？如果您将一个对象或数组分配给一个`const`，您可以操纵它。

```
const user = {
  id: 1,
  name: 'Antonin Januska',
  active: false,
  monthsSinceLastActive: 3,
}

const inactiveUsers = [user];

if (user.monthsSinceLastActive < 10) {
  user.active = true; // this works!
  inactiveUsers.pop(); // works, too
}

user = getNextUser(); // fails because we're assigning a new object
inactiveUsers = [user]; // this would fail, too 
```

Enter fullscreen mode Exit fullscreen mode

### 什么时候用哪个？

这是一个争议的来源。大家都有看法。

#### var 还是 let？

我通常看不到使用`var`的理由，许多开发者也会同意。即使在`var`有用的情况下(比如获取 for 循环的最后一个值)，编写变通方法也是更明确的。还是那句话，这些是我的个人观点:

```
let lastValueLet = 0;

for (let i = 0; i < 10; i++) {
  // I keep expecting this to error out
  // because of redeclaration
  var lastValueVar = i;
  lastValueLet = i;
}

// I'd get tripped up by this, does it exist?
// what would be the value?
console.log(lastValueVar); 

// very clear that it exists
console.log(lastValueLet); 
```

Enter fullscreen mode Exit fullscreen mode

结论:我尽可能默认`let`。

#### 常量？

常数更有争议。其中一个原因是因为`object`的问题，你可以改变对象，尽管它是一个常量。请记住，常数防止重新分配，而不是突变！

关于这一点，我看到了两个阵营/意识形态:

##### 真常数

常量只能用于数字、字符串等的*和*。但不是对象。它们应该用于声明某种配置或数学常数(如简写`PI` ):

```
const PI = 3.14; 

function calculateArea(radius) {
  return radius * PI * PI;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个阵营通常推荐大写常量，这在其他语言中也是一样:

```
const MAX_HEALTH_VALUE = 100;

function drinkHealthPotion(currentHP, restoreAmount) {
  if (currentHP + restoreAmount > MAX_HEALTH_VALUE) {
    return MAX_HEALTH_VALUE;
  }

  return currentHP + restoreAmount;
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 【请勿触摸】常量

有了对常量警告的充分了解，第二个阵营更关心的是*意图*，并推荐使用`const`来表明你不打算改变那个变量。这通常在析构提供给函数的参数或声明函数表达式时使用:

```
const sumFunctionExpression = (num1, num2) => num1 + num2;

function getUserId(user) {
  const { id } = user;

  return id;
} 
```

Enter fullscreen mode Exit fullscreen mode

本质上，使用`const`是为了你不希望其他开发者在将来接触你的代码时改变的任何事情。作为你自己的一个指示，你不想改变这个变量。

这是我个人的喜好。

## 你呢？关于函数和变量声明，你个人的习惯是什么？