# 什么是 JavaScript 提升？

> 原文：<https://dev.to/kris/what-is-javascript-hoisting-kol>

#### 对 JS 吊装有了更清晰的认识，更好的学习编码

[![](img/87cec7c2a6f5046756df05e4b69a115e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ank8wpgt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/660/1%2ABgVNlXW6I6_1bWr8m8Ypsg.png)

**提升**是 JS 默认的行为，在代码执行之前，在作用域的顶部定义所有的声明。提升的好处之一是，它使我们能够在函数出现在代码中之前调用它们。JavaScript 只提升声明，不提升初始化。你可以在这里找到官方文件。

**提示**:用[**Bit**](https://bit.dev)([GitHub](https://github.com/teambit/bit))将可复用的 JS 代码变成共享组件。Bit 帮助每个人构建**模块化 JavaScript 应用**，在项目和团队之间轻松共享组件，并且**构建得更好&更快**

[共享可重用代码组件作为团队成员](https://bit.dev)

#### 了解 JavaScript 到底是什么提升

变量声明和初始化按以下顺序进行:

**声明->初始化/赋值->用途**

```
// Variable lifecycle
let x; // Declaration
x = “hoisting”; // Assignment
console.log(x); // Usage 
```

然而，由于 JavaScript 允许同时声明和初始化变量，这是最常用的模式:

```
let x = “hoisting”; 
```

最重要的是，您应该始终记住 JavaScript 首先在后台声明变量。然后，初始化它们。因此，知道变量声明的处理发生在任何代码执行之前也是很好的。然而，在执行给它们赋值的代码之前，JavaScript 中不存在未声明的变量。

因此，当执行赋值时，赋给未声明变量的值会隐式地将其创建为全局变量。这指定所有未声明的变量都是全局变量。

```
// hoisting
function Hoisting(){
  x = 100;
  let y = 200;
}
Hoisting();
console.log(x); // 100
console.log(y); // Reference Error: y is not defined 
```

在上面的代码示例中，有一个函数叫做*提升*()。因此，我们有一个没有使用 let/var/const 声明的变量和一个 let 变量 *y* 。将未声明的变量赋给全局范围是由 JavaScript 完成的。但是对于变量 *y* ，我们得到一个引用错误。

#### 在 ES5 中托管

在 ES5 中，我们考虑 *var* 关键字。与 let/const 相比，用 *var* 提升有些不同。用 *var* 的例子来看看吊装是如何工作的:

```
var num (global)
console.log(car); // undefined
var car = ‘Lamborgini’; 
```

在上面的代码中，当记录在使用变量名之后才声明和赋值的变量名时，编译器给出一个“未定义”的结果。这是意料之外的，因为我们应该在声明之前就尝试使用 *car* 变量。

但是解释者对此有不同的看法，如下:

```
//how interpreter sees the above code
var car;
console.log(car); // undefined
car = ‘Lamborgini’; 
```

#### 托管在函数作用域的变量中

让我们通过下面的例子来学习函数作用域变量的提升是如何发生的:

```
//function scoped
function myFunc(){
  console.log(car);
  var car = ‘Lamborgini’;
}
myFunc(); // undefined 
```

但是，与声明全局变量的代码相比，没有什么不同。解释器提供的结果是“未定义”。

```
//function scoped
function myFunc(){
 var car;
 console.log(car);
 car = ‘Lamborgoni’;
}
myFunc(); // undefined 
```

为了减轻这种情况，我们可以确保在使用变量之前，同时声明和赋值变量。因此，下面的代码在顶部显示了声明:

```
function myFunc(){
  var car = ‘Lamborgini’;
  console.log(car) // Lamborgini
}  
myFunc(); 
```

#### 吊装于 ES6

我们可以看到下面的例子:

```
let num(global)
console.log(num);
let num = 003; // ReferencError: num is not defined 
```

在 *var* 关键字中，我们期望日志的输出是未定义的。然而，由于 es6 中的 *let* 不允许使用未声明的变量，解释器会显式抛出一个引用错误。这确保了我们总是首先声明变量。

***让*和*保持*关键词。**

用 let 或 const 声明的变量和常量不吊！

javascript 初始化不是托管的

JavaScript 只提升声明，不提升初始化。

```
var a = “volkswagon”; // Initialize a
var b = “Lamborgini”; // Initialize b
elem = document.getElementById("car"); // Find an element
elem.innerHTML = a + " " + b; // Display a and b as volkswagon and lamborgini 
```

在上面的代码中，由于变量的声明发生在结果之前。结果，代码的执行打印出变量 *a* 和 *b* 的结果。

```
var a = “i10”; // Initialize a
elem = document.getElementById("car"); // Find an element
elem.innerHTML = "a is " + a + “ and b is " + b; // Display a and b
var b = “Lamborgini”; // Initialize b

Result:
a is i10 and b is undefined 
```

因此，这是因为只将声明( *var b* )提升到顶部，而不是初始化(= "Lamborgini ")。因为提升， *b* 在使用前已经声明，但是因为初始化没有提升， *b* 的值没有定义。

#### 吊装班

JavaScript 类可以分为两类:

*   类声明
*   类别表达式

在类声明中

它们很像功能对等物。因此，这意味着不需要提升 JavaScript 类声明。但是，在求值之前，它们保持未初始化状态。因此，这实际上意味着您必须在使用类之前声明它。

```
var car1 = new car();
car1.height = 5;
car1.weight = 500;
console.log(car1); // Output: ReferenceError: car is not defined
class car{
  constructor(height, weight) {
    this.height = height;
    this.weight = weight;
  }
} 
```

在上面的代码中，出现了引用错误。这是因为，在初始化 *car1* 变量之后，定义 *car* 类就发生了。为了解决这个错误，我们只需要在 *car1* 初始化之前定义 *car* 类。这是类声明中的宿主。

```
class car{
  constructor(height, weight) {
    this.height = height;
    this.weight = weight;
  }
}

var car1 = new car();
car1.height = 5;
car1.weight = 500;
console.log(car1); 
```

因此，这给出了正确的结果。

在类表达式中

它们很像它们的功能对等物。因此，这意味着没有提升类表达式。

因此，下面是一个未命名或匿名的类表达式变体的例子:

```
var rect = new shapes();
rect.height = 10;
rect.width = 20;
console.log(rect); // Output: TypeError: shapes is not a constructor

var shapes = class {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}; 
```

因此，正确的做法是这样的:

```
var shapes = class {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};

var rect = new shapes();
rect.height = 10;
rect.width = 20;
console.log(rect); 
```

### 总结

对于许多开发人员来说，提升是一种未知的 JavaScript 行为。许多开发人员也忽视了它的重要性。此外，如果一个开发人员不了解提升，程序可能会包含 bug(错误)。为了避免错误，总是在每个作用域的开始声明所有变量。因此，这就是 JavaScript 解释代码的方式，这是一个很好的规则。

### 了解更多

*   【2019 年 11 个 Javascript 数据可视化库
*   [2019 年你应该知道的 11 个 Javascript 实用程序库](https://blog.bitsrc.io/11-javascript-utility-libraries-you-should-know-in-2018-3646fb31ade)
*   【2019 年 6 个 JavaScript 用户认证库

* * *